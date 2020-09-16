---
title: include file
description: include file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
---

## Prerequisites
Before you get started, make sure to:
- Create an Azure account with an active subscription. For details, see [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Install [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Create an Azure Communication Services resource. For details, see [Create an Azure Communication Resource](../../create-communication-resource.md). You'll need to record your resource **endpoint** for this quickstart.
- Obtain a [User Access Token](../../user-access-tokens.md) to enable the chat client.

## Setting up

### Create a new C# application

In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app with the name `ChatQuickstart`. This command creates a simple "Hello World" C# project with a single source file: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Change your directory to the newly created app folder and use the `dotnet build` command to compile your application.

```console
cd ChatQuickstart
dotnet build
```

### Install the package

Install the Azure Communication Chat client library for .NET

```PowerShell
dotnet add package Azure.Communication.Chat
``` 

## Object model

The following classes handle some of the major features of the Azure Communication Services Chat client library for C#.

| Name                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| [ChatClient](../../../references/overview.md) | This class is needed for the Chat functionality. You instantiate it with your subscription information, and use it to create, get and delete threads. |
| [ChatThreadClient](../../../references/overview.md) | This class is needed for the Chat Thread functionality. You obtain an instance via the ChatClient, and use it to send/receive/update/delete messages, add/remove/get users, send typing notifications and read receipts. |

## Create a chat client

To create a chat client, you'll use the Communications Service endpoint and the access token that was generated as part of pre-requisite steps. User access tokens enable you to build client applications that directly authenticate to Azure Communication Services. Once you generate these tokens on your server, pass them back to a client device. You need to use the CommunicationUserCredential class from the Common client library to pass the token to your chat client.

!NOTE Ensure that your user access token has 'chat' scope.

```csharp
// Your unique Azure Communication service endpoint
System.Uri endpoint = System.Uri("https://<RESOURCE_NAME>.communicationservices.azure.com");
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential("<USER_ACCESS_TOKEN>");
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## Start a chat thread

Use the `createChatThread` method to create a chat thread.
- Use `topic` to give a topic to this chat; Topic can be updated after the chat thread is created using the `UpdateThread` function.
- Use `members` to list the thread members to be added to the thread. The member associated with the access token that was generated as part of pre-requisite steps should be part of the list.

The response `chatThreadClient` is used to perform operations on the created chat thread: adding members to the chat thread, sending a message, deleting a message, etc.
It contains the `Id` attribute which is the unique ID of the chat thread. 

!NOTE Replace <MEMBER_ID_#> with the generated user's ID as demonstrated in the [User Access Token](../../user-access-tokens.md) tutorial.

```csharp
var topic = "Chat Thread topic C# sdk";

var member1 = new ChatThreadMember(new CommunicationUser("<MEMBER_ID_1>"));
member1.DisplayName = "display name member 1";

var member2 = new ChatThreadMember(new CommunicationUser("<MEMBER_ID_2>"));

var members = new List<ChatThreadMember>
{
    member1,
    member2    
};

ChatThreadClient chatThreadClient = chatClient.CreateChatThread(topic, members);
var threadId = chatThreadClient.Id;
```

## Get a chat thread client
The `GetChatThreadClient` method returns a thread client for a thread that already exists. It can be used for performing operations on the created thread: add members, send message, etc. threadId is the unique ID of the existing chat thread.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## Send a message to a chat thread

Use `SendMessage` method to send a message to a thread identified by threadId.

- Use `content` to provide the chat message content, it is required.
- Use `priority` to specify the message priority level, such as 'Normal' or 'High', if not specified, 'Normal' will be used.
- Use `senderDisplayName` to specify the display name of the sender, if not specified, empty name will be used.

`SendChatMessageResult` is the response returned from sending a message, it contains an id, which is the unique ID of the message.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = chatThreadClient.SendMessage(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## Receive chat messages from a chat thread

You can retrieve chat messages by polling the `GetMessages` method on the chat thread client at specified intervals.

```csharp
Pageable<ChatMessage> allMessages = chatThreadClient.GetMessages();
var maxPageSize = 3; // Number of messages per page
string continuationToken = null; // Opaque url that contains a link to be used by the client library to retrieve the next page from the API 

foreach (Page<ChatMessage> page in allMessages.AsPages(continuationToken, maxPageSize))
{
	foreach (ChatMessage chatMessage in page.Values)
	{
		Console.WriteLine(chatMessage.Id);
		Console.WriteLine(chatMessage.Content);
	}
	continuationToken = page.ContinuationToken;
}
```

`GetMessages` returns the latest version of the message, including any edits or deletes that happened to the message using `UpdateMessage` and `DeleteMessage`. For deleted messages, `chatMessage.DeletedOn` returns a datetime value indicating when that message was deleted. For edited messages, `chatMessage.EditedOn` returns a datetime indicating when the message was edited. The original time of message creation can be accessed using `chatMessage.CreatedOn`, and it can be used for ordering the messages.

`GetMessages` returns different types of messages which can be identified by `chatMessage.Type`. These types are:

- `Text`: Regular chat message sent by a thread member.

- `ThreadActivity/TopicUpdate`: System message that indicates the topic has been updated.

- `ThreadActivity/AddMember`: System message that indicates one or more members have been added to the chat thread.

- `ThreadActivity/DeleteMember`: System message that indicates a member has been removed from the chat thread.

For more details, see [Message Types](../../../concepts/chat/concepts.md#message-types).


## Add a user as member to the chat thread

Once a thread is created, you can then add and remove users from it. By adding users, you give them access to be able to send messages to the thread, and add/remove other members. Before calling `AddMembers`, ensure that you have acquired a new access token and identity for that user. The user will need that access token in order to initialize their chat client.

Use `AddMembers` method to add thread members to the thread identified by threadId.

 - Use `members` to list the members to be added to the chat thread;
 - `User`, required, is the identity you get for this new user.
 - `DisplayName`, optional, is the display name for the thread member.
 - `ShareHistoryTime`, optional, time from which the chat history is shared with the member. To share history since the beginning of chat thread, set it to DateTime.MinValue. To share no history, previous to when the member was added, set it to the current time. To share partial history, set it to a point in time between the thread creation and the current time.

```csharp
var member1 = new ChatThreadMember(new CommunicationUser("<MEMBER_ID_1>"));
member1.DisplayName = "display name member 1";
member1.ShareHistoryTime = DateTime.MinValue; // share all history
var members = new List<ChatThreadMember>
{
    member1
};
chatThreadClient.AddMembers(members);
```
## Remove user from a chat thread

Similar to adding a user to a thread, you can remove users from a chat thread. To do that, you need to track the identity (CommunicationUser) of the members you have added.

Use `RemoveMember`, where `memberId` is the ID of the member to be removed from the thread.

```csharp
string memberId = "<MEMBER_ID_1>";
chatThreadClient.RemoveMember(new CommunicationUser(memberId));
```

## Run the code

Run the application from your application directory with the `dotnet run` command.

```console
dotnet run
```

