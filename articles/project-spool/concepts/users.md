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
# User Access Tokens

Azure Communication Services retains its own directory of user entities which engage in ACS powered communication. User entities engage chat threads and interact with real-time data, voice, and video communication.

ACS’s separate identity system is oriented towards flexibility and simplicity. While you may simply make communication identities mapping to existing entities in a Azure Active Directory deployment, you can also make communication identities for any abstract user concept interfacing with the ACS dataplane. 

Example 1: Bob sends Sue a chat message directly
In this example an end-user device is going to directly send a chat message from a human, bob@contoso.com, to another human, sue@contoso.com. The steps are:

***Trusted Service***
1. Create a TokenCredential for an AAD service principal (webservice.contoso.com) to access the ACS control plane 
2. Create an ACS Communication Client with the Management capability
3. Create a TokenCredential.CommunicationCredential for bob@contoso.com 
4. Share this TokenCredential with Bob’s end-user device as a string

***Bob’s end-user device***
1. Create an ACS Communication Client with the Chat capability, using the string-encoded credential provided by the trusted service as an initialization parameter 
2.  Send a message to sue@contoso.com 

### Use this TokenCredential to create an ACS Communication Client with the Management capability

Trusted services can access ACS on behalf of a AAD service principal or managed identity. For more information see the authorization topic. For clarity of the mananagement samples on this page, below is example code for creating a management client from a trusted service:

```
CommunicationClient serviceClient = new CommunicationClient(
                new Capabilities().AddManagement(),
                new Uri("https://contoso.westus.acs.azure.net"),
          serviceToken); 
```

## Creating users
Communication identity only need to be provisioned once in the lifetime of the ACS deployment.
```
var identity = serviceClient.Management().CreateUser(“Bob@contoso.com”);
```
## Issuing user access tokens


### Create a TokenCredential for bob@contoso.com 
Using it’s management enabled communication client, the trusted service creates a TokenCredential.CommunicationCredential for the communication identity associated with Bob’s user principal name.
```
userToken = await serviceClient.Management().UserTokens.CreateTokensAsync(bob@contoso.com)
```
This will implicitly create an identity for bob@contoso.com if there is not an already created communication identity.

Communication identities are uniquely identified by their name, an application provided string that must be unique across the ACS deployment.  and their type: human, bot, phone number, etc.  Attempting to create an identity that already exists returns an IdentityAlreadyExists exception.

A common pattern is using user principal names (UPN) as the identifying name. For example you may have an end-user application that uses Microsoft Graph APIs to identify interesting communication endpoints and leverage UPNs as a common currency across these systems with guaranteed uniqueness. 

### Share this TokenCredential with Bob’s end-user device
ACS communication identity token credentials can be JSON encoded for transmission between the trusted service generating them and the end-user device using them. 

ACS communication identity tokens typically have a lifetime of 12 hours. They can be refreshed directly by the end-user device, this means that the end-user device does not need to re-request a token from the trusted service intermediary in typical expiration scenarios. 

```string bobEncodedToken = userToken.GetToken();```

## Using user access tokens for communication 

The Azure Communication Services client SDKs require user access tokens to authenticate against 

### Use this TokenCredential to create an ACS Communication Client with the Chat capability

Bob creates a token credential object using the string they obtained from the trusted service. They use this token to create a Chat enabled communication client.
```var client = new CommunicationClient(
                new Capabilities().AddChat(),
                new Uri("https://contoso.westus.acs.azure.net"),
                bobEncodedToken);
```

#### Send a message to sue@contoso.com 
We need a source identity for Bob and target endpoint for Sue, this is trivial using their unique communication identity names, which are UPNs in this example. Using that target endpoint we send a message from Bob’s CommunicationClient. 
```
var message = await client.ServiceChat().SendMessage(“Hi Sue.”, 
                 new Azure.Communication.Identity(“bob@contoso.com”),
                 new Azure.Communication.Identity(“sue@contoso.com”)
);
```

It is possible with ACS for a communication identity to communicate on behalf of another user, a typical scenario being business administrators. In these situations there is little change to the above example.


## User access token scopes

Scopes enable you to limit the functionality available to a .user when creating access tokens. 

```csharp
// TODO code sample to create user access token with optional scopes
```

Scopes are applied to individual user access token. If you wish to remove a user's ability to access to a some specific functionality, you should create a first [revoke any existing access tokens](#revoking-user-access-tokens) that may include undesired scopes before issueing a new token with a more limited set of scopes.

### Supported scopes:

Azure Communication Services supports the following scopes for user access tokens (TODO I made all these up):

| Name                   | Description  |
| -----------------------|--------------|
| `chat:send_message`    | Grants the ability to send chat messages |
| `chat:manage_threads`  | Grants the create new chat threads and add users to chat threads |
| `voip:adhoc`           | Grants the ability to make outbound VOIP calls |
| `voip:pstn`            | Grants the ability to make outbound PSTN calls using with the calling SDK |
| `rooms:manage`            | Grants the ability to make outbound PSTN calls using with the calling SDK |



## Access token life cycle (AKA refreshing access tokens)

User access tokens are short lived credentials that need to be rereshed in order to prevent your users from experiencing service disruptions. The client SDKs provide events to let you know when the provided user access token is about to expire. You should subcribe to these events and use them to fetch a new user access token from your trusted service.

```javascript
const { ChatClient } = require('@azure/communicationservices-chat');

// Your unique Azure Communication service endpoint
const endpoint = 'https://<RESOURCE_NAME>.communcationservices.azure.com';

// User access token fetched from your trusted service
const userAccessToken = 'SECRET';

// Initialize the a chat client
const client = new ChatClient(endpoint, userAccessToken);

client.on('token_will_expire', (tokenProvider) => {
  // fetch a new token from the your trusted service and
  // pass it to the token provider
  fetchNewToken()
    .then((newToken) => tokenProvider.udpateToken(newToken));
});
```

## Revoking users access tokens



## Impersonating users
In some cases you may want a user to communicate on behalf of another user. There are no explicit flows for impersonation with ACS, and you accomplish impersonation by providing a user another person's user access token.
