---
title: ios Group
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_group.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（EaseIM App） experience

Download link：[download page](http://www.easemob.com/download/im)

# Message

Message: IM interaction entity, the corresponding type in the SDK is **EMMessage**. **EMMessage** consists of 
EMMessageBody.

The Easemob SDK header files involved in the message are as follows:

``` objc
// Message building
EMMessage.h
EMMessageBody.h
EMTextMessageBody.h
EMImageMessageBody.h
EMVoiceMessageBody.h
EMVideoMessageBody.h
EMFileMessageBody.h
EMLocationMessageBody.h
EMCmdMessageBody.h

// The message method calling, such as adding proxy, removing proxy, sending messages, etc. It also includes conversation related operations, such as creating or obtaining a conversation, obtaining a list of conversation, etc.
IEMChatManager.h

// The callback method of the protocol of the message, such as the callback method of listening and receiving messages, etc.
EMChatManagerDelegate.h
```

## Construct message

Description of the "Initialize Message Instance" method used in the following example of constructing a message:

``` objc
/*!
 *  Initialize the message instance
 *
 *  @param aConversationId  Conversation ID
 *  @param aFrom            sender
 *  @param aTo              receiver
 *  @param aBody            message body instance
 *  @param aExt             extended information
 *
 *  @result Message instance
 */
- (id)initWithConversationID:(NSString *)aConversationId
                        from:(NSString *)aFrom
                          to:(NSString *)aTo
                        body:(EMMessageBody *)aBody
                         ext:(NSDictionary *)aExt;
 
```

aConversationId:
Conversation id. For example, if a sends a message to b, then the SDK will generate a conversation on the side of a. The conversation id is b. When constructing the message, aConversationId is consistent with to .

to: Represents the Easemob id of the receiver.

from: Represents the currently logged-in EMClient ID, generally use \[EMClient
sharedClient\].currentUsername; method to get.

`Note: If you are sending a message to a group or chat room, then aConversationId and to should be replaced by the group id or chat room id`

In general, the currently logged-in Easemob id should send a message to which Easemob id or group id or chat room id.

### Construct a text message

``` objc
/*!
 *  Initialization of the text message body
 *
 *  @param aText   Text content
 *  
 *  @result Text message body instant
 */
- (instancetype)initWithText:(NSString *)aText;

// Calling:
EMTextMessageBody *body = [[EMTextMessageBody alloc] initWithText:@"Message to send"];
// Get the currently logged-in Easemob id
NSString *from = [[EMClient sharedClient] currentUsername];

// Generate Message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct emoticons

Sending emoticons is actually sending text messages. After receiving the text message, the receiver first check whether the text message is an emoticon message, and if it is, the text message is displayed as a corresponding emoticon picture. You can use [emoji standard](https://unicode.org/emoji/charts/full-emoji-list.html) to create emoji pictures and mapping corresponding to text strings . You can also maintain emoji pictures and the mapping corresponding to text strings 

``` objc
/*!
 *  Initialize of emoticon message body
 *
 *  @param aText  Emoticon message text string
 *  
 *  @result  Emoticon message body instant
 */
- (instancetype)initWithText:(NSString *)aText;

// Calling:
EMTextMessageBody *body = [[EMTextMessageBody alloc] initWithText:@"The emoticon message text string to be sent"];
// Get the currently logged-in Easemob id
NSString *from = [[EMClient sharedClient] currentUsername];

//Generate Message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct a picture message

``` objc
/*!
 *  Initialization of file message body--------------------------
 *
 *  @param aData        Attachment data
 *  @param aDisplayName Attachment display name （not include path)
 *
 *  @result Message body instance
 */
- (instancetype)initWithData:(NSData *)aData
                 displayName:(NSString *)aDisplayName;
                 
// Calling:               
EMImageMessageBody *body = [[EMImageMessageBody alloc] initWithData:data displayName:@"image.png"];
// body.compressionRatio = 1.0f; 1.0 means the original image is sent without compression. The default value is 0.6, and the compression factor is 0.6 times
NSString *from = [[EMClient sharedClient] currentUsername];

//Generate Message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct location message

``` objc
/*!
 *  Initialize the location message body
 *
 *  @param aLatitude   latitude
 *  @param aLongitude  longitude
 *  @param aAddress    geographic location information
 *  
 *  @result Location message body instant
 */
- (instancetype)initWithLatitude:(double)aLatitude
                       longitude:(double)aLongitude
                         address:(NSString *)aAddress;
                         
// Calling:                         
EMLocationMessageBody *body = [[EMLocationMessageBody alloc] initWithLatitude:39 longitude:116 address:@"Address"];
NSString *from = [[EMClient sharedClient] currentUsername];

// Generate message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct a voice message

``` objc
/*!
 *  Initialization of file message body
 *
 *  @param aLocalPath   Attachment local path
 *  @param aDisplayName Attachment display name (does not include path)
 *
 *  @result Message body instance
 */
- (instancetype)initWithLocalPath:(NSString *)aLocalPath
                      displayName:(NSString *)aDisplayName;

// Calling:                      
EMVoiceMessageBody *body = [[EMVoiceMessageBody alloc] initWithLocalPath:@"audioPath" displayName:@"audio"];
body.duration = duration;
NSString *from = [[EMClient sharedClient] currentUsername];

// Generate message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct a video message

``` objc
/*!
 *  Initialization of file message body
 *
 *  @param aLocalPath   Attachment local path
 *  @param aDisplayName Attachment display name (not include path)
 *
 *  @result Message body instance
 */
- (instancetype)initWithLocalPath:(NSString *)aLocalPath
                      displayName:(NSString *)aDisplayName;
                      
// Calling:                      
EMVideoMessageBody *body = [[EMVideoMessageBody alloc] initWithLocalPath:@"videoPath" displayName:@"video.mp4"];
NSString *from = [[EMClient sharedClient] currentUsername];

// Generate message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Structure file message

``` objc
/*!
 *  Initialization of file message body
 *
 *  @param aLocalPath   Attachment local path
 *  @param aDisplayName Attachment display name (not include path)
 *
 *  @result Message body instance
 */
- (instancetype)initWithLocalPath:(NSString *)aLocalPath
                      displayName:(NSString *)aDisplayName;
                      
// Calling:                      
EMFileMessageBody *body = [[EMFileMessageBody alloc] initWithLocalPath:@"filePath" displayName:@"file"];
NSString *from = [[EMClient sharedClient] currentUsername];

// generate message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct a pass-through message

A special type of message provided by the SDK, namely CMD, will not store db, nor will it be pushed through APNS, similar to a command-type message. For example, if your server wants to notify the client to perform specific operations, you can ask the server and the client to agree on a certain field in advance, and when the client receives the agreed field, perform a certain special operation. In addition, actions beginning with "em\_" and "easemob::" are internal reserved fields, so be careful not to use them

``` objc
/*!
 *  Initialize the command message body
 *  After receiving the user-defined string, parse the user-defined string, and then know that something has been sent.
 *  ex. The user wants to share location, the string here can be "loc", after parsing "loc", you know that this message is a location sharing message, and then other information can be placed in the .ext attribute to parse.
 *  ex. If the user needs to perform the "snapchat" function, here you can write a string yourself, such as "Snap", and then bring the messageid to be deleted into the .ext, and the receiver can delete the corresponding after receiving it to achieve the function of "snapchat"
 *
 *  @param aAction  Command content
 *  
 *  @result Command message body instant
 */
- (instancetype)initWithAction:(NSString *)aAction;

// Calling:
EMCmdMessageBody *body = [[EMCmdMessageBody alloc] initWithAction:action];
NSString *from = [[EMClient sharedClient] currentUsername];

// generate message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct a custom type message

`SDK support of 3.6.5 and above`

In addition to the above types of messages, users can define their own message types to have the benefits the user's business.
The custom message type supports users to set a message type name by themselves, so that users can add a variety of custom messages.
The content of the custom message is in key and value format, and the user needs to add and parse the content by himself.

``` objc
// event is a custom message event that needs to be delivered, such as a gift message, you can set event = @"gift"
// The params type is NSDictionary<String, String>
EMCustomMessageBody *body = [[EMCustomMessageBody alloc] initWithEvent:event ext: params];
NSString *from = [[EMClient sharedClient] currentUsername];

//Generate Message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];  // ext:Extended message section
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Construct extended message

When the message types provided by the SDK do not meet the requirements, developers can generate the message types they need by extending the text, voice, picture, location and other message types provided by the SDK.

`Key value type must be NSString, Value value type must be NSString or NSNumber type BOOL, int, unsigned in, long long, double`

Here is an extended text message. If this custom message needs to use voice or pictures, it can be extended from voice, picture messages, or location messages.

``` objc
// Take a single chat message as an example
EMTextMessageBody *body = [[EMTextMessageBody alloc] initWithText:@"Message to send"];
NSString *from = [[EMClient sharedClient] currentUsername];

//generate Message
NSDictionary *messageExt = @{@"key":@"value"};
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:messageExt];  // ext:Extended message section
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message
```

### Insert message

#### Method One

Insert directly, inserting in this way will not verify the existence of the EMConversation object where the message is located, and insert the message directly into the database

``` objc
EMTextMessageBody *body = [[EMTextMessageBody alloc] initWithText:@"Message to insert"];
NSString *from = [[EMClient sharedClient] currentUsername];

//generate Message
EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:messageExt];
message.chatType = EMChatTypeChat;// Set as single chat message
//message.chatType = EMChatTypeGroupChat;// Set as group chat message
//message.chatType = EMChatTypeChatRoom;// Set as chat room message

[[EMClient sharedClient].chatManager importMessages:@[message] completion:^(EMError *aError) {
    if (!aError) {
        NSLog(@"Import a set of messages to DB successfully");
    } else {
        NSLog(@"Reasons for failure to import a set of messages to DB --- %@", aError.errorDescription);
    }
}];
```

#### Method Two

This insertion method will verify the existence of the EMConversation object where it is located. After insertion, the attribute in the EMConversation object will be updated, such as LatestMessage


    EMTextMessageBody *body = [[EMTextMessageBody alloc] initWithText:@"Message to insert"];
    NSString *from = [[EMClient sharedClient] currentUsername];
        
    //generate Message
    EMMessage *message = [[EMMessage alloc] initWithConversationID:@"6001" from:from to:@"6001" body:body ext:nil];
    message.chatType = EMChatTypeChat;// Set as single chat message
    //message.chatType = EMChatTypeGroupChat;// Set as group chat message
    //message.chatType = EMChatTypeChatRoom;// Set as chat room message
    //message.timestamp = 1509689222137; Message time
    EMConversation *conversation =  [[EMClient sharedClient].chatManager getConversation:message.conversationId type:EMConversationTypeChat createIfNotExist:YES];
        // type: Conversation type
        //      EMConversationTypeChat // single chat
        //      EMConversationTypeGroupChat // group chat
        //      EMConversationTypeChatRoom // chat room
    [conversation insertMessage:message error:nil];

### Update message attribute

``` objc
/*!
 *  Update message to DB
 *
 *  @param aMessage         Messages
 *  @param aCompletionBlock The completed callback
 */
- (void)updateMessage:(EMMessage *)aMessage
           completion:(void (^)(EMMessage *aMessage, EMError *aError))aCompletionBlock;

//Call:
[[EMClient sharedClient].chatManager updateMessage:nil completion:^(EMMessage *aMessage, EMError *aError) {
    if (!aError) {
        NSLog(@"Update message to DB successfully");
    } else {
        NSLog(@"The reason for the failure to update the message to the DB --- %@", aError.errorDescription);
    }
}];
```

## Conversation

The Easemob SDK header files involved in the session are as follows:

``` objc
// Conversation, including session id, session type, etc.
EMConversation.h

// Conversation method call, including creating or obtaining session, obtaining session list, etc.
IEMChatManager.h

```

Conversation：The container for operating the chat message **EMMessage**, the corresponding type in the SDK is**EMConversation**.

### Create/Get a Conversation

Create a conversation based on conversationId.

``` objc
/*!
 *  Get a conversation
 *
 *  @param aConversationId  Conversation id
 *  @param aType            Conversation type
 *  @param aIfCreate        if does not exist, create
 *
 *  @result Conversation object
 */
- (EMConversation *)getConversation:(NSString *)aConversationId
                               type:(EMConversationType)aType
                   createIfNotExist:(BOOL)aIfCreate;
                   
// Call:   

aConversationId:
The Easemob id of the receiver in a single chat conversation---------
The group id of the group conversations to send messages to the group
The chat room ID of the chat room which messages are sent to

aType:
//EMConversationTypeChat            single chat
//EMConversationTypeGroupChat       group chat
//EMConversationTypeChatRoom        chat room
                
[[EMClient sharedClient].chatManager getConversation:@"8001" type:EMConversationTypeChat createIfNotExist:YES];
```

### Delete conversation

#### Delete a single conversation

``` objc
/*!
 *  Delete conversation
 *
 *  @param aConversationId      Conversation ID
 *  @param aIsDeleteMessages    Whether to delete messages in the conversation
 *  @param aCompletionBlock     the completed callback
 */
- (void)deleteConversation:(NSString *)aConversationId
          isDeleteMessages:(BOOL)aIsDeleteMessages
                completion:(void (^)(NSString *aConversationId, EMError *aError))aCompletionBlock;
                
// Call:
[[EMClient sharedClient].chatManager deleteConversation:@"8001" isDeleteMessages:YES completion:^(NSString *aConversationId, EMError *aError) {
    if (!aError) {
        NSLog(@"Conversation deleted successfully");
    } else {
        NSLog(@"Reasons for the failure to delete the Conversation--- %@", aError.errorDescription);
    }
}];                
```

#### Batch delete conversations based on conversationId

``` objc
/*!
 *  Delete a batch of conversations
 *
 *  @param aConversations       Conversation list <EMConversation>
 *  @param aIsDeleteMessages    Whether to delete messages in the conversation
 *  @param aCompletionBlock     the completed callback
 */
- (void)deleteConversations:(NSArray *)aConversations
           isDeleteMessages:(BOOL)aIsDeleteMessages
                 completion:(void (^)(EMError *aError))aCompletionBlock;
                 
                 
EMConversation *conversation = [[EMClient sharedClient].chatManager getConversation:@"8001" type:EMConversationTypeChat createIfNotExist:NO];
// Call:
[[EMClient sharedClient].chatManager deleteConversations:@[conversation] isDeleteMessages:YES completion:^(EMError *aError) {
    if (!aError) {
        NSLog(@"A batch of Conversation deleted successfully");
    } else {
        NSLog(@"Reasons for the failure to delete a batch of Conversation --- %@", aError.errorDescription);
    }
}];
```

### Get conversations list

``` objc
/*!
 *  Get all conversation, if they do not exist in memory, they will be loaded from DB
 *
 *  @result Conversation list<EMConversation>
 */
- (NSArray *)getAllConversations;

// Call:
NSArray *conversations = [[EMClient sharedClient].chatManager getAllConversations];
```

### Get the number of unread messages in a single Conversation

``` objc
/*!
 *  \~chinese
 *  Get a conversation
 *
 *  @param aConversationId  conversation ID
 *  @param aType            conversation type
 *  @param aIfCreate        if does not exist, create
 *
 *  @result Conversation object
 */
- (EMConversation *)getConversation:(NSString *)aConversationId
                               type:(EMConversationType)aType
                   createIfNotExist:(BOOL)aIfCreate;
                   
// Get the number of unread messages in a single chat conversation                   
EMConversation *conversation = [[EMClient sharedClient].chatManager getConversation:@"8001" type:EMConversationTypeChat createIfNotExist:YES];
[conversation unreadMessagesCount];

// Get the number of unread messages in a group chat conversation                   
EMConversation *conversation = [[EMClient sharedClient].chatManager getConversation:@"121828583195137" type:EMConversationTypeGroupChat createIfNotExist:YES];
[conversation unreadMessagesCount];
```

### Get the number of unread messages in all conversations

The SDK currently does not provide a method to directly obtain the number of unread messages in all conversations. It can only obtain the conversation list first, and then traverse the number of unread messages in each conversation and accumulate them to calculate the number of unread messages in all conversations.

``` objc
NSArray *conversations = [[EMClient sharedClient].chatManager getAllConversations];
NSInteger unreadCount = 0;
for (EMConversation *conversation in conversations) {
    unreadCount += conversation.unreadMessagesCount;
}
```

### Message retrieval

You can retrieve messages in a certain conversation by keyword, message type, and start and end time. Use the EMConversation session object to call.

``` objc
/*!
 *  Get the specified number of messages from the database, the retrieved messages are sorted by time, and do not contain the referenced message, if the ID of the referenced message is null, then the latest message will be fetched
 *
 *  @param aMessageId       ID of the reference message
 *  @param count            Number of records obtained
 *  @param aDirection       Message search direction
 *  @param aCompletionBlock the completed callback
 */
- (void)loadMessagesStartFromId:(NSString *)aMessageId
                          count:(int)aCount
                searchDirection:(EMMessageSearchDirection)aDirection
                     completion:(void (^)(NSArray *aMessages, EMError *aError))aCompletionBlock;
                     
// Call:
EMConversation *conversation = [[EMClient sharedClient].chatManager getConversation:@"8001" type:EMConversationTypeChat createIfNotExist:YES];
[conversation loadMessagesStartFromId:messageId count:10 searchDirection:EMMessageSearchDirectionUp completion:^(NSArray *aMessages, EMError *aError) {
    if (!aError) {
        // EMMessage is stored in the aMessage array
        NSLog(@"get the message from the database Successfully --- %@", aMessages);
    } else {
        NSLog(@"Reasons for the failure to delete the message from the database--- %@", aError.errorDescription);
    }
}];

/*!
 *  Get the specified type of messages from the database, and the retrieved messages are sorted by time. If the reference timestamp is a negative number, it will be taken from the latest message. If aCount is less than or equal to 0, it will be treated as 1
 *
 *  @param aType            Message type
 *  @param aTimestamp       Reference timestamp
 *  @param aCount           Number of records obtained
 *  @param aUsername        The sender, if it is null, it will be neglected
 *  @param aDirection       Message search direction
 *  @param aCompletionBlock the completed callback
 */
- (void)loadMessagesWithType:(EMMessageBodyType)aType
                   timestamp:(long long)aTimestamp
                       count:(int)aCount
                    fromUser:(NSString*)aUsername
             searchDirection:(EMMessageSearchDirection)aDirection
                  completion:(void (^)(NSArray *aMessages, EMError *aError))aCompletionBlock;

/*!
 *  Get the message containing the specified content from the database, and the fetched messages are sorted by time. If the reference timestamp is a negative number, it will be fetched from the latest message forward. If aCount is less than or equal to 0, it will be treated as 1
 *
 *  @param aKeywords        Search keyword. if it is null, it will be neglected
 *  @param aTimestamp       Reference timestamp
 *  @param aCount           Number of records obtained
 *  @param aSender          The sender. if it is null, it will be neglected
 *  @param aDirection       Message search direction
 *  @param aCompletionBlock the completed callback
 */
- (void)loadMessagesWithKeyword:(NSString*)aKeyword
                      timestamp:(long long)aTimestamp
                          count:(int)aCount
                       fromUser:(NSString*)aSender
                searchDirection:(EMMessageSearchDirection)aDirection
                     completion:(void (^)(NSArray *aMessages, EMError *aError))aCompletionBlock;

/*!
 *  Get the messages within a specified time period from the database, and the retrieved messages are sorted by time. In order to prevent taking up too much memory, the user should set the maximum number of loaded messages
 *
 *  @param aStartTimestamp  start time in Millisecond
 *  @param aEndTimestamp    End Time
 *  @param aCount           Maximum number of loaded messages
 *  @param aCompletionBlock the Completed callback
 */
- (void)loadMessagesFrom:(long long)aStartTimestamp
                      to:(long long)aEndTimestamp
                   count:(int)aCount
              completion:(void (^)(NSArray *aMessages, EMError *aError))aCompletionBlock;

```

### Global Message retrieval

The messages in all conversations can be retrieved by message type and keywords. Use \[EMClient
sharedClient\].chatManager singleton Call.

``` objc
/*!
 *  Get the specified type of messages from the database, and the retrieved messages are sorted by time. If the reference timestamp is a negative number, it will be taken from the latest message. If aCount is less than or equal to 0, it will be treated as 1
 *
 *  @param aType            Message type
 *  @param aTimestamp       Reference timestamp
 *  @param aCount           Number of records obtained
 *  @param aUsername        The sender, if it is null, it will be neglected
 *  @param aDirection       Message search direction
 *  @param aCompletionBlock the completed callback
 */
- (void)loadMessagesWithType:(EMMessageBodyType)aType
                   timestamp:(long long)aTimestamp
                       count:(int)aCount
                    fromUser:(NSString*)aUsername
             searchDirection:(EMMessageSearchDirection)aDirection
                  completion:(void (^)(NSArray *aMessages, EMError *aError))aCompletionBlock;
// Call:
[[EMClient sharedClient].chatManager loadMessagesWithKeyword:@"Hello" timestamp:1575997248290 count:10 fromUser:nil searchDirection:EMMessageSearchDirectionUp completion:^(NSArray *aMessages, EMError *aError) {
    if (!aError) {
        // EMMessage is stored in the aMessage array
        NSLog(@"get the message from the database Successfully--- %@", aMessages);
    } else {
        NSLog(@"Reasons for the failure to delete the message from the database --- %@", aError.errorDescription);
    }
}];
                  
/*!
 *  Get the message containing the specified content from the database, and the fetched messages are sorted by time. If the reference timestamp is a negative number, it will be fetched from the latest message forward. If aCount is less than or equal to 0, it will be treated as 1
 *
 *  @param aKeywords        Search keyword. if it is null, it will be neglected
 *  @param aTimestamp       Reference timestamp
 *  @param aCount           Number of records obtained
 *  @param aSender          The sender. if it is null, it will be neglected
 *  @param aDirection       Message search direction
 *  @param aCompletionBlock the completed callback     
 */
- (void)loadMessagesWithKeyword:(NSString*)aKeywords
                      timestamp:(long long)aTimestamp
                          count:(int)aCount
                       fromUser:(NSString*)aSender
                searchDirection:(EMMessageSearchDirection)aDirection
                     completion:(void (^)(NSArray *aMessages, EMError *aError))aCompletionBlock;
// Call:
[[EMClient sharedClient].chatManager loadMessagesWithType:EMMessageBodyTypeText timestamp:1575997248290 count:10 fromUser:nil searchDirection:EMMessageSearchDirectionUp completion:^(NSArray *aMessages, EMError *aError) {
    if (!aError) {
        // EMMessage is stored in the aMessage array
        NSLog(@"get the specified message from the database Successfully --- %@", aMessages);
    } else {
        NSLog(@"Reasons for the failure to delete the specified message from the database --- %@", aError.errorDescription);
    }
}];
                                  
```

## Chat

The chat operation can only be performed after the successful login. When sending messages, single chat and group chat call have a unified interface, the difference is only to set the message.chatType.

### Send a message

``` objc
/*!
 *  Send a message
 *
 *  @param aMessage         message
 *  @param aProgressBlock   Attachment upload progress callback block
 *  @param aCompletionBlock Send finished callback block
 */
- (void)sendMessage:(EMMessage *)aMessage
           progress:(void (^)(int progress))aProgressBlock
         completion:(void (^)(EMMessage *message, EMError *error))aCompletionBlock;

//Call:
[[EMClient sharedClient].chatManager sendMessage:message progress:^(int progress) {
    NSLog(@"Attachment upload progress --- %d", progress);
} completion:^(EMMessage *message, EMError *error) {
    if (!error) {
        NSLog(@"Message sent successfully");
    } else {
        NSLog(@"The reason for the failure to send the message --- %@", error.errorDescription);
    }
}];
```

### Receive message

``` objc
protocol:EMChatManagerDelegate

proxy:
// Registered message 
[[EMClient sharedClient].chatManager addDelegate:self delegateQueue:nil];

// Remove message proxy
[[EMClient sharedClient].chatManager removeDelegate:self];

```

Receiving ordinary messages will use the following callbacks:

If you use the chat page in the demo of the Easemob during integration, the message proxy has been registered in the chat page, and the callback method for receiving common messages is monitored, and no other addition is required.

If you use chat page written by yourself during integration, you need to register your own message proxy in the chat page to monitor the callback method for receiving common messages.

In addition, it is recommended to register the message agent in the root controller of your own project, monitor the callback method for receiving common messages, and use it for ringtones or local notifications for receiving messages when you are not on the chat page.

``` objc
/*!
 @method
 @brief receive one or more non-cmd messages
 */
- (void)messagesDidReceive:(NSArray *)aMessages;
```

When receiving a pass-through (cmd) message, the following callbacks will be used:

``` objc
/*!
 @method
 @brief receive one or more non-cmd messages
 */
- (void)cmdMessagesDidReceive:(NSArray *)aCmdMessages;
```

### Parse common messages (including custom type messages)

``` objc
// Callback for receiving a message. Messages with attachments can be downloaded using the method of downloading attachments provided by the SDK (the details will be mentioned later)
- (void)messagesDidReceive:(NSArray *)aMessages {
    for (EMMessage *message in aMessages) {
    EMMessageBody *msgBody = message.body;
    switch (msgBody.type) {
        case EMMessageBodyTypeText:
        {
            // Text message received
            EMTextMessageBody *textBody = (EMTextMessageBody *)msgBody;
            NSString *txt = textBody.text;
            NSLog(@"The text received is txt -- %@",txt);
        }
        break;
        case EMMessageBodyTypeImage:
        {
            // Get a picture message body
            EMImageMessageBody *body = ((EMImageMessageBody *)msgBody);
            NSLog(@"remote path of Large image  -- %@"   ,body.remotePath);
            NSLog(@"local path of Large image  -- %@"    ,body.localPath); // // Need to use the download method provided by the SDK to exist
            NSLog(@"The secret of the large picture -- %@"    ,body.secretKey);
            NSLog(@"W of large pic -- %f ,H of large pic -- %f",body.size.width,body.size.height);
            NSLog(@"Download status of the large picture -- %lu",body.downloadStatus);


            // 
            NSLog(@"remote path of small image -- %@"   ,body.thumbnailRemotePath);
            NSLog(@"local path of small image -- %@"    ,body.thumbnailLocalPath);
            NSLog(@"secret of small image -- %@"    ,body.thumbnailSecretKey);
            NSLog(@"W of small pic -- %f ,H of small picture -- %f",body.thumbnailSize.width,body.thumbnailSize.height);
            NSLog(@"Download status of the small picture -- %lu",body.thumbnailDownloadStatus);
        }
        break;
        case EMMessageBodyTypeLocation:
        {
            EMLocationMessageBody *body = (EMLocationMessageBody *)msgBody;
            NSLog(@"latitude-- %f",body.latitude);
            NSLog(@"longitude-- %f",body.longitude);
            NSLog(@"address-- %@",body.address);
            }
            break;
        case EMMessageBodyTypeVoice:
        {
            // sdk will automatically download audio
            EMVoiceMessageBody *body = (EMVoiceMessageBody *)msgBody;
            NSLog(@"Audio remote path -- %@"      ,body.remotePath);
            NSLog(@"Audio local path -- %@"       ,body.localPath); // it will only exist after the download method provided by the sdk is used (the audio will automatically call)
            NSLog(@"Audio secret -- %@"        ,body.secretKey);
            NSLog(@"Audio file size -- %lld"       ,body.fileLength);
            NSLog(@"Audio file download status -- %lu"   ,body.downloadStatus);
            NSLog(@"Audio duration -- %lu"      ,body.duration);
        }
        break;
        case EMMessageBodyTypeVideo:
        {
            EMVideoMessageBody *body = (EMVideoMessageBody *)msgBody;

            NSLog(@"Video remote path -- %@"      ,body.remotePath);
            NSLog(@"Video local path-- %@"       ,body.localPath); // it will only exist after the download method provided by the sdk is used
            NSLog(@"Video secret -- %@"        ,body.secretKey);
            NSLog(@"Video file size -- %lld"       ,body.fileLength);
            NSLog(@"Download status of video files -- %lu"   ,body.downloadStatus);
            NSLog(@"The length of the video -- %lu"      ,body.duration);
            NSLog(@"W of video -- %f ,H of video -- %f", body.thumbnailSize.width, body.thumbnailSize.height);

            // sdk will automatically download thumbnails
            NSLog(@"The remote path of the thumbnail -- %@"     ,body.thumbnailRemotePath);
            NSLog(@"The local path of the thumbnail -- %@"      ,body.thumbnailLocalPath);
            NSLog(@"Thumbnail secret -- %@"        ,body.thumbnailSecretKey);
            NSLog(@"Thumbnail download status -- %lu"      ,body.thumbnailDownloadStatus);
        }
        break;
        case EMMessageBodyTypeFile:
        {
            EMFileMessageBody *body = (EMFileMessageBody *)msgBody;
            NSLog(@"File remote path -- %@"      ,body.remotePath);
            NSLog(@"File local path -- %@"       ,body.localPath); // it will only exist after the download method provided by the sdk is used
            NSLog(@"File secret -- %@"        ,body.secretKey);
            NSLog(@"File size -- %lld"       ,body.fileLength);
            NSLog(@"File download status-- %lu"   ,body.downloadStatus);
        }
        break;
        case EMMessageBodyTypeCustom:
        {
            // Custom type message received
            EMCustomMessageBody *body = (EMCustomMessageBody *)msgBody;
            NSLog(@"event -- %@", body.event);
            NSLog(@"ext -- %@", body.ext);
        }
        break;

        default:
        break;
    }
    }
}
```

### Parse the pass-through message

``` objc
- (void)cmdMessagesDidReceive:(NSArray *)aCmdMessages {
    for (EMMessage *message in aCmdMessages) {
        EMCmdMessageBody *body = (EMCmdMessageBody *)message.body;
         NSLog(@"The action received is -- %@",body.action);
    }    
}
```

### Parse message extended attributes

``` objc
- (void)cmdMessagesDidReceive:(NSArray *)aCmdMessages {
    for (EMMessage *message in aCmdMessages) {
        // Extended attributes in cmd messages
        NSDictionary *ext = message.ext;
        NSLog(@"The extended attributes in the cmd message are-- %@",ext)
    }    
}
// Message received callback
- (void)messagesDidReceive:(NSArray *)aMessages {
    for (EMMessage *message in aMessages) {
        // Extended attributes in the message
        NSDictionary *ext = message.ext;
        NSLog(@"The extended attributes in the message are -- %@",ext);
    }
}
```

### Automatically download attachments in messages

After the SDK receives the message, it will download by default: the thumbnail of the image message, the voice of the voice message, and the first frame of the video of the video message.

**Please first judge that the attachment you want to download is not downloaded successfully, then download the method under Call, otherwise the SDK download method will get the attachment from the server again. **

``` objc
/*!
 *  Download the thumbnail (the thumbnail of the picture message or the first frame of the video message). The SDK will automatically download the thumbnail, so unless the automatic download fails, the user does not need to download the thumbnail by himself
 *
 *  @param aMessage            Message
 *  @param aProgressBlock      Attachment download progress callback block
 *  @param aCompletionBlock    Download complete callback block
 */
- (void)downloadMessageThumbnail:(EMMessage *)aMessage
                        progress:(void (^)(int progress))aProgressBlock
                      completion:(void (^)(EMMessage *message, EMError *error))aCompletionBlock;
                      
// Call:                      
[[EMClient sharedClient].chatManager downloadMessageThumbnail:message progress:nil completion:^(EMMessage *message, EMError *error) {
    if (!error) {
        NSLog(@"Thumbnail downloaded successfully");
    } else {
        NSLog(@"Reasons for failure to download thumbnails ---%@",error.errorDescription);
    }
}];
```

### Download the original attachment in the message

``` objc
/*!
 *  Download message attachments (voice, video, original picture, file). The SDK will automatically download the voice message, so unless the automatic download of the voice fails, the user does not need to download the voice attachment by himself
 *
 *  Asynchronous method
 *
 *  @param aMessage            Message
 *  @param aProgressBlock      Attachment download progress callback block
 *  @param aCompletionBlock    Download complete callback block
 */
[[EMClient sharedClient].chatManager downloadMessageAttachment:message progress:nil completion:^(EMMessage *message, EMError *error) {
        if (!error) {
        NSLog(@"Download the message attachment successfully");
    } else {
        NSLog(@"Reasons for failure to download message attachments --- %@",error.errorDescription);
    }
}];
```

### Message delivered receipt

The SDK provides a delivery receipt. When the other party receives your message, you will receive the following callback

``` objc
/*!
 @method
 @brief Received one or more delivery receipts
 */
- (void)messagesDidDeliver:(NSArray *)aMessages;
```

### Message read receipt

The read receipt requires the developer to actively call back. When the user reads the message, the developer actively call back the methods.
The message read receipt function is currently only available for single chat (ChatType.Chat). The recommended solution is conversation read receipt (conversation ack) combined with a single message read receipt (read ack), which can reduce the amount of read ack messages sent
**`Note: The group message read receipt function is a value-added service. For specific usage, please skip to the group message read receipt. `**

#### Send read receipt

It is recommended to send the conversation ack first when entering the conversation

``` objc
[[EMClient sharedClient].chatManager ackConversationRead:@"conversation id" completion:nil];
```

On the conversation page, when a message is received, you can send a message read ack according to the message type

``` objc
/*!
 *  Send message read receipt
 *
 *  Asynchronous method
 *
 *  @param aMessage             Message id
 *  @param aUsername            Receiver of read messages
 *  @param aCompletionBlock     Completed callback
 */
- (void)sendMessageReadAck:(NSString *)aMessageId
                    toUser:(NSString *)aUsername
                completion:(void (^)(EMError *aError))aCompletionBlock;
                
// Call:                
// Send a read receipt. It is written here just to show the progress of sending, and the developer needs to decide where to send it in the APP.
[[EMClient sharedClient].chatManager sendMessageReadAck:@"messageId" toUser:@"username" completion:^(EMError *aError) {
    if (!aError) {
         NSLog(@"Successfully sent the read receipt");
    } else {
        NSLog(@"Reasons for failure to send read receipt --- %@", aError.errorDescription);
    }
}];
```

#### Receive read receipt

Receive conversation read receipt

``` objc
/**
 * \~chinese
 * Conversation read callback received
 *
 * @param from  CHANNEL_ACK sender
 * @param to      CHANNEL_ACK receiver
 *
 * \~english
 * received conversation read ack
 * @param from  the username who send channel_ack
 * @param to      the username who receive channel_ack
 */
- (void)onConversationRead:(NSString *)from to:(NSString *)to;
```

After receiving the callback of the conversation read receipt（channel ack）, the SDK will set the session-related messages as read by the other party. After receiving this callback, you need to perform page refresh and other operations

Receive message read receipt

``` objc
/*!
 *  Received one or more read receipts
 *
 *  @param aMessages  Message list<EMMessage>
 */
- (void)messagesDidRead:(NSArray *)aMessages;
```

### Set whether the group message needs a read receipt (value-added service)

When the message is a group message, the message sender (currently the administrator and the group owner) can set whether the message needs a read receipt, if necessary, set the EMMessage attribute isNeedGroupAck to YES, and then send it.

    @property (nonatomic) BOOL isNeedGroupAck;

### Send group message read receipt

``` objc
/*!
 *  \~chinese
 *  Send group message read receipt
 *
 *  Asynchronous method
 *
 *  @param aMessageId           Message id
 *  @param aGroupId             group id
 *  @param aContent             attachment content
 *  @param aCompletionBlock     Completed callback
 *
 *  \~english
 *  Send read acknowledgement for message
 *
 *  @param aMessageId           Message id
 *  @param aGroupId             group receiver
 *  @param aContent             Content
 *  @param aCompletionBlock     The callback of completion block
 *
 */
- (void)sendGroupMessageReadAck:(NSString *)aMessageId
                        toGroup:(NSString *)aGroupId
                        content:(NSString *)aContent
                     completion:(void (^)(EMError *aError))aCompletionBlock;
                     
    // Call
    // Send group message read receipt. It is written here just to show the progress of sending. The developer needs to decide where to send it in the APP.
    [[EMClient sharedClient].chatManager sendGroupMessageReadAck:@"messageId"
                                                         toGroup:@"GroupId"
                                                         content:@"Receipt content"
                                                      completion:^(EMError *aError)
    {
        if (!aError) {
            NSLog(@"sent the read receipt Successfully");
        } else {
            NSLog(@"Reasons for failure to send read receipt --- %@", aError.errorDescription);
        }
    }];
```

After sending the group read receipt, the groupAckCount attribute of EMMessage corresponding to message sender will change;

``` objc
@property (nonatomic, readonly) int groupAckCount;
```

### Group message read callback

``` objc
/*!
 *  \~chinese
 *  Receipt of group message read receipt
 *
 *  @param aMessages  List of read messages<EMGroupMessageAck>
 *
 *  \~english
 *  Invoked when receiving read acknowledgement in message list
 *
 *  @param aMessages  Acknowledged message list<EMGroupMessageAck>
 */
- (void)groupMessageDidRead:(EMMessage *)aMessage
                  groupAcks:(NSArray *)aGroupAcks;
```

### Get group read details

    /**
     *  \~chinese
     *  get the read receipt of the specified group from the server
     *
     *  Asynchronous method
     *
     *  @param  aMessageId           The message id to be get
     *  @param  aGroupId             The group id corresponding to the receipt To get
     *  @param  aGroupAckId          Group receipt id to go back
     *  @param  aPageSize            Get the number of messages
     *  @param  aCompletionBlock     Get the callback for the end of the message
     */
    - (void)asyncFetchGroupMessageAcksFromServer:(NSString *)aMessageId
                                         groupId:(NSString *)aGroupId
                                 startGroupAckId:(NSString *)aGroupAckId
                                        pageSize:(int)aPageSize
                                      completion:(void (^)(EMCursorResult *aResult, EMError *error, int totalCount))aCompletionBlock;

### Message roaming

The SDK provides an interface for obtaining historical information from the server

``` objc
/**
 *  get the history of the specified Conversation from the server
 *
 *  Asynchronous method
 *
 *  @param  aConversationId     Conversation id of the roaming message to get
 *  @param  aConversationType   Conversation type roaming message to get
 *  @param  aStartMessageId     refers to the ID of the start message
 *  @param  aPageSize           Get the number of messages (up to 50 messages at a time)
 *  @param  aCompletionBlock    Get the callback for the end of the message
 */
- (void)asyncFetchHistoryMessagesFromServer:(NSString *)aConversationId
                           conversationType:(EMConversationType)aConversationType
                             startMessageId:(NSString *)aStartMessageId
                                   pageSize:(int)aPageSize
                                 complation:(void (^)(EMCursorResult *aResult, EMError *aError))aCompletionBlock;
                                 
// Call:
[[EMClient sharedClient].chatManager asyncFetchHistoryMessagesFromServer:@"6001" conversationType:EMConversationTypeChat startMessageId:messageId pageSize:10 completion:^(EMCursorResult *aResult, EMError *aError) {
    if (!aError) {
        NSLog(@"Get the message from the server successfully");
    } else {
        NSLog(@"The reason for the failure to get the message from the server --- %@", aError.errorDescription);
    }
}];
```

### Message withdrawn

``` objc
/*!
*  Withdraw message
*
*  Asynchronous method
*
*  @param aMessageId           Message Id
*  @param aCompletionBlock     Completed callback
*/
- (void)recallMessageWithMessageId:(NSString *)aMessageId
                        completion:(void (^)(EMError *aError))aCompletionBlock;
           
// Call:
[[EMClient sharedClient].chatManager recallMessageWithMessageId:messageId completion:^(EMError *aError) {
    if (!aError) {
        NSLog(@"Withdraw the message successfully");
    } else {
        NSLog(@"Reasons for failure to withdraw the message--- %@", aError.errorDescription);
    }
}];
```

Message withdrawal receipt

``` objc
/*!
 *  withdrawn Received message 

 *
 *  @param aMessages  Withdraw messages list<EMMessage>
 */
- (void)messagesDidRecall:(NSArray *)aMessages;
```

Note: The message withdrawal is a value-added function, please contact Easemob Business Opening.

------------------------------------------------------------------------

\<WRAP group> \<WRAP half column> Previous Page：[iOS SDK
Update log](/im/ios/sdk/releasenote) \</WRAP>

\<WRAP half column> Next Page：[Friends management](/im/ios/basics/buddy) \</WRAP>
\</WRAP>