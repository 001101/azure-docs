---
title: User Access Tokens
description: Learn how to manage users and authenticate them to ACS
author: mikben
manager: jken
services: azure-project-spool

ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-project-spool


---

# User access tokens

User access tokens enable you to build client applications that directly authenticate to Azure Communication Services. You generate these tokens on your server, pass them back to a client device, and then use them to initialize the Communication Services SDKs.

## Creating user access tokens

The Azure Communication Services Configuration SDK provides the functionality to create user access tokens. You should expose this functionality via a trusted web service that your users can authenticate against. Once your endpoint validates that a user should be authorized to access Azure Communication Services, you should use the configuration SDK to create a User Access Token for that user's unique identity, serialize it and return it to the user's client application so it can initialize an instance of an Azure Communication Services client SDK.

```csharp
[HttpPost]
[Authorize]
public async Task<ActionResult> CreateAccessToken(string userName)
{
    // validate the user that sent this request is authorized to
    // access Azure Communication Services
    
    // initialize the configuration client with a connection string
    // retrieved from the Azure Portal
    var configurationClient = new ConfigurationClient(CONNECTION_STRING);
    
    // create a user access token for the provided identity
    var tokenResult = await configurationClient.CreateUserAccessTokenAsync(userName);
    
    // TODO: we should handle failures
    
    // return the access token to the
    var response = new CreateAccessTokenResponse()
    {
      token: tokenResult.UserAccessToken,
      ttl: tokenResult.Ttl,
    }
    return Ok(response);
}
```

By default, user access tokens expire after 24 hours but it is a good security practice to limit the lifetime to the minimum duration required by your application. You can use the optional `ttl` parameter of the `CreateUserAccessTokenAsync` method to limit the duration of user access tokens.

## User access token scopes

Scopes allow you to specify the exact Azure Communications Services functionality that a user access token will be able to authorize. By default, user access tokens enable clients to participate in chat threads they have been invited to and to receive incoming VOIP calls. Additional scopes must be specified when creating user access tokens.

```csharp
// create a user access token that enables outbound voip calling
// and expires after five minutes
var tokenResult = await configurationClient.CreateUserAccessTokenAsync(
    userName,
    ["voip:adhoc"],
    (60 * 5),
);
```

Scopes are applied to individual user access tokens. If you wish to remove a user's ability to access to some specific functionality, you should first [revoke any existing access tokens](#revoking-user-access-tokens) that may include undesired scopes before issuing a new token with a more limited set of scopes.

### Supported Scopes

Azure Communication Services supports the following scopes for user access tokens:

| Name                  | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| `chat:message`        | Grants the ability to participate in chat threads            |
| `chat:manage_threads` | Grants the ability create and invite users to chat threads   |
| `voip:adhoc`          | Grants the ability to make outbound VOIP calls using the calling SDK |
| `voip:pstn`           | Grants the ability to make outbound PSTN calls using the calling SDK |
| `rooms:manage`        | Grants the ability to create and invite users to rooms       |

> NOTE: These scopes are illustrative. We currently are not planning to support `chat:manage_threads`, but will continue to discuss offline.
> |nmurav: why only Outbound calls for PSTN and VoiP? We also allow recieving the inbound calls



## Reissuing user access tokens

User access tokens are short-lived credentials that need to be reissued in order to prevent your users from experiencing service disruptions. The client SDKs provide an proactive-refresh callback to let you update the user access token before it is about to expire. You should provide a refresh callback and use it to fetch a new user access token from your trusted service.

```javascript
import { CommunicationUserCredential } from '@azure/communicationservices-common';
import { ChatClient } from '@azure/communicationservices-chat';
import { fetchNewToken } from 'myTokenHelper';

// Your unique Azure Communication service endpoint
const endpoint = 'https://<RESOURCE_NAME>.communcationservices.azure.com';

// Create a CommunicationUserCredential and pass a refresh function. The refresh function is called on first usage of the credential and again when the token is about to expire.
const userCredential = new CommunicationUserCredential(async () => fetchNewToken(userName));

// Initialize the chat client
const client = new ChatClient(endpoint, userCredential);
```



User access tokens are valid for 24 hours by default. If your application isn't running long-running sessions, you can decide to not use the built-in refresh callback and just pass the token string to the client that you want to instantiate. You won't get notified before the token expires.


```javascript
import { ChatClient } from '@azure/communicationservices-chat';

// Your unique Azure Communication service endpoint
const endpoint = 'https://<RESOURCE_NAME>.communcationservices.azure.com';

// User access token fetched from your trusted service
const userAccessToken = 'SECRET';

// Initialize the chat client
const client = new ChatClient(endpoint, userAccessToken);
```



If your client application is using several of the Azure Communication Services client SDKs, you should instantiate each SDK with a shared instance of the `CommunicationUserCredential` class and use that instance to manage the user access token reissuing process.

```javascript
import { CommunicationUserCredential } from '@azure/communicationservices-common';
import { ChatClient } from '@azure/communicationservices-chat';
import { CallingFactory } from '@azure/communicationservices-calling';
import { fetchNewToken } from 'myTokenHelper';

const userCredential = new CommunicationUserCredential(async() => fetchNewToken(userName));

// initialize the chat SDK with the token provider
const chatClient = new ChatClient(resourceUrl, userCredential);

// initialize the calling SDK with the token provider
const callingClient = await CallingFactory.create(userCredential);
```

## Lazy loading user access tokens

By default, the Azure Communication Services SDKs cache user access tokens in memory. This works well for long-running client applications but is not optimized for implementing communication functionality in a stateless web service (e.g. a chatbot).

Instead, you should initialize the `CommunicationUserCredential` with the aforementioned refresh callback which enables you to fetch user access tokens on demand. With the refresh callback you can then use a caching strategy that suites your application architecture. The following snippet assumes that you have implemented your own functions to fetch from and write to a cache.

```csharp
var userCredential = new CommunicationUserCredential(async () => {
    // try to fetch the access token from the application cache
    var token = await FetchTokenFromCache(userName);
    if (!string.IsNullOrEmpty(token))
    {
        return token;
    }
    
    // create a new token using the configuration SDK 
    var tokenResult = await configurationClient.CreateUserAccessTokenAsync(userName);

    // set the token in the cache for the next request
    await CacheAccessToken(
        key: username, 
        value: tokenResult.token, 
        ttl: tokenResult.ttl
    );

    return tokenResult.token;
});
```

> [!CAUTION] 
> Tokens are sensitive data, because they grant access to a user's resources. Therefore, it's critical to protect tokens from being compromised. If your custom caching logic involves writing user access tokens to a backing store, it is strongly reccomended that you use encryption.

## Revoking user access tokens

In some cases, you may need to explicitly revoke user access tokens, for example, when a user changes the password they use to authenticate to your service. This functionality is available via the Azure Communication Services Configuration SDK.

```csharp
// revoke all access tokens issued for a given user
var result = await configurationClient.RevokeUserAccessTokenAsync(userName);
```

## Troubleshooting

> TODO: common issues and how to deal with them
