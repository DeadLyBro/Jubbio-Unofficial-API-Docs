# Unofficial Jubbio API Documentation

This document provides language-agnostic documentation for the Jubbio platform API, including REST Endpoints, WebSocket Gateway Events and Voice API.

## Contributing

We strive to keep this documentation accurate and up-to-date. If you find any errors, missing information, or have suggestions for improvements, feel free to contribute! You can:

- **Report Issues**: Open an issue on our GitHub repository to let us know about any problems you've encountered.
- **Submit Updates**: Help improve the documentation by submitting a pull request with your changes or additions.

Your contributions are greatly appreciated and help make this resource better for everyone.

## Authentication

All API requests require authentication using a bot token. Include the token in the `Authorization` header:

```js
Authorization: Bot YOUR_BOT_TOKEN
```

## Base URL

The default API base URL is;
```
https://gateway.jubbio.com/api/v1
```

The WebSocket gateway URL is; 
```
wss://gateway.jubbio.com
```

## REST API

The REST API allows you to interact with guilds, channels, messages, users, and other resources.

### Messages

#### Create Message

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages
```

**Method:** POST

**Parameters:**
- `guild_id` (string): The guild ID
- `channel_id` (string): The channel ID

**Content-Type:** application/json

**Request Body:**
```json
{
  "content": "Hello World!",
  "embeds": [], // optional
  "components": [], // optional
  "mentions": { // optional
    "users": [{"id": 123, "username": "user"}],
    "roles": [{"id": "456", "name": "role"}],
    "everyone": false
  },
  "files": [], // optional
  "message_reference": { // optional
    "message_id": "789"
  }
}
```

**Response (200 OK):**
```json
{
  "id": "message_id",
  "channel_id": "channel_id",
  "guild_id": "guild_id", // optional
  "author": {
    "id": "bot_id",
    "username": "bot_username",
    "display_name": "Bot Display Name",
    "avatar_url": "https://example.com/avatar.png",
    "bot": true
  },
  "user_id": 123, // optional
  "content": "Hello World!",
  "timestamp": "2023-01-01T00:00:00.000Z",
  "edited_timestamp": null, // optional
  "attachments": [], // optional
  "embeds": [] // optional
}
```

#### Upload Attachment

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/attachments
```

**Method:** POST

**Content-Type:** multipart/form-data

**Form Data:**
- `file` (file): The file to upload

**Response (201 Created):**
```json
{
  "id": "attachment_id",
  "url": "https://example.com/attachment.png",
  "filename": "attachment.png"
}
```

#### Create Message with File

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages
```

**Method:** POST

**Parameters:**
- `guild_id` (string): The guild ID
- `channel_id` (string): The channel ID

**Content-Type:** application/json

**Request Body:**
```json
{
  "content": "Message with file",
  "file": {
    "name": "file.png",
    "data": "base64_or_buffer",
    "contentType": "image/png" // optional
  }
}
```

**Response (200 OK):**
```json
{
  "id": "message_id",
  "channel_id": "channel_id",
  "guild_id": "guild_id",
  "author": {
    "id": "bot_id",
    "username": "bot_username",
    "bot": true
  },
  "content": "Message with file",
  "timestamp": "2023-01-01T00:00:00.000Z",
  "attachments": [
    {
      "id": "attachment_id",
      "filename": "file.png",
      "size": 12345,
      "url": "https://example.com/file.png"
    }
  ]
}
```

#### Create Ephemeral Message

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages
```

**Method:** POST

**Parameters:**
- `guild_id` (string): The guild ID
- `channel_id` (string): The channel ID

**Content-Type:** application/json

**Request Body:**
```json
{
  "content": "This message is only visible to you!",
  "embeds": [] // optional
}
```

**Response (200 OK):**
```json
{
  "id": "message_id",
  "ephemeral": true,
  "flags": 64
}
```

#### Create DM Message

**Endpoint:**

```
/bot/channels/{channel_id}/messages
```

**Method:** POST

**Parameters:**
- `channel_id` (string): The DM channel ID

**Content-Type:** application/json

**Request Body:**
```json
{
  "content": "Hello in DM!",
  "embeds": [] // optional
}
```

**Response (200 OK):**
```json
{
  "id": "message_id",
  "channel_id": "channel_id",
  "author": {
    "id": "bot_id",
    "username": "bot_username",
    "bot": true
  },
  "content": "Hello in DM!",
  "timestamp": "2023-01-01T00:00:00.000Z"
}
```

#### Edit Message

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages/{message_id}
```

**Method:** PATCH

**Parameters:**
- `guild_id` (string)
- `channel_id` (string)
- `message_id` (string)

**Content-Type:** application/json

**Request Body:**
```json
{
  "content": "Updated message", // optional
  "embeds": [], // optional
  "components": [] // optional
}
```

**Response (200 OK):**
```json
{
  "id": "message_id",
  "channel_id": "channel_id",
  "guild_id": "guild_id", // optional
  "author": {
    "id": "bot_id",
    "username": "bot_username",
    "display_name": "Bot Display Name",
    "avatar_url": "https://example.com/avatar.png",
    "bot": true
  },
  "user_id": 123, // optional
  "content": "Updated message",
  "timestamp": "2023-01-01T00:00:00.000Z",
  "edited_timestamp": "2023-01-01T00:01:00.000Z",
  "attachments": [], // optional
  "embeds": [] // optional
}
```

#### Delete Message

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages/{message_id}
```

**Method:** DELETE

**Parameters:**
- `guild_id` (string)
- `channel_id` (string)
- `message_id` (string)

**Response:** 204 No Content

#### Get Messages

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages
```

**Method:** GET

**Query Parameters:**
- `limit` (number, optional): Number of messages to retrieve (default 50, max 100)
- `before` (string, optional): Message ID to get messages before
- `after` (string, optional): Message ID to get messages after

**Response (200 OK):**
```json
[
  {
    "id": "message_id_1",
    "channel_id": "channel_id",
    "guild_id": "guild_id", // optional
    "author": {
      "id": "user_id",
      "username": "username",
      "display_name": "Display Name",
      "avatar_url": "https://example.com/avatar.png",
      "bot": false
    },
    "user_id": 123, // optional
    "content": "Message content",
    "timestamp": "2023-01-01T00:00:00.000Z",
    "edited_timestamp": null, // optional
    "attachments": [], // optional
    "embeds": [] // optional
  }
]
```

#### Add Reaction

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages/{message_id}/reactions/{emoji}
```

**Method:** PUT

**Parameters:**
- `guild_id` (string)
- `channel_id` (string)
- `message_id` (string)
- `emoji` (string): Emoji to react with

**Response:** 204 No Content

#### Remove Reaction

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages/{message_id}/reactions/{emoji}/@me
```

**Method:** DELETE

**Response:** 204 No Content

#### Get Pinned Messages

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/pins
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "id": "message_id",
    "channel_id": "channel_id",
    "guild_id": "guild_id",
    "author": {
      "id": "user_id",
      "username": "username",
      "display_name": "Display Name"
    },
    "content": "Pinned message",
    "timestamp": "2023-01-01T00:00:00.000Z",
    "attachments": [],
    "embeds": []
  }
]
```

#### Delete Message

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages/{message_id}
```

**Method:** DELETE

**Response:** 204 No Content

#### Add Reaction

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages/{message_id}/reactions/{emoji}/@me
```

**Method:** PUT

**Response:** 204 No Content

#### Pin Message

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/pins/{message_id}
```

**Method:** PUT

**Response:** 204 No Content

#### Unpin Message

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/pins/{message_id}
```

**Method:** DELETE

**Response:** 204 No Content

#### Bulk Delete Messages

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/messages/bulk-delete
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "messages": ["message_id_1", "message_id_2"]
}
```

**Response:** 204 No Content

### Channels

#### Get Guild Channels

**Endpoint:**

```
/bot/guilds/{guild_id}/channels
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "id": "channel_id",
    "type": 0,
    "guild_id": "guild_id",
    "name": "general",
    "topic": "General discussion",
    "position": 0,
    "parent_id": null
  }
]
```

#### Create Channel

**Endpoint:**

```
/bot/guilds/{guild_id}/channels
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "name": "new-channel",
  "type": 0,
  "parent_id": "category_id", // optional
  "permission_overwrites": [] // optional
}
```

**Response (201 Created):**
```json
{
  "id": "new_channel_id",
  "type": 0,
  "guild_id": "guild_id",
  "name": "new-channel",
  "topic": null, // optional
  "position": 1,
  "parent_id": "category_id" // optional
}
```

#### Delete Channel

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}
```

**Method:** DELETE

**Response:** 204 No Content

#### Delete Category

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{category_id}
```

**Method:** DELETE

**Response:** 204 No Content

#### Edit Channel Permissions

**Endpoint:**

```
/bot/channels/{channel_id}/permissions/{overwrite_id}
```

**Method:** PUT

**Content-Type:** application/json

**Request Body:**
```json
{
  "allow": "permissions_bit",
  "deny": "permissions_bit",
  "type": 0
}
```

**Response:** 204 No Content

#### Delete Channel Permission

**Endpoint:**

```
/bot/channels/{channel_id}/permissions/{overwrite_id}
```

**Method:** DELETE

**Response:** 204 No Content

### Guild Members

#### Get Member

**Endpoint:**

```
/bot/guilds/{guild_id}/members/{user_id}
```

**Method:** GET

**Response (200 OK):**
```json
{
  "user": {
    "id": "user_id",
    "username": "username",
    "display_name": "Display Name",
    "avatar_url": "https://example.com/avatar.png",
    "bot": false
  },
  "nick": "Nickname",
  "avatar": null,
  "roles": ["role_id_1", "role_id_2"],
  "joined_at": "2023-01-01T00:00:00.000Z",
  "deaf": false,
  "mute": false,
  "permissions": "permissions_bit",
  "voice": {
    "channel_id": null,
    "self_mute": false,
    "self_deaf": false
  }
}
```

#### Timeout Member

**Endpoint:**

```
/bot/guilds/{guild_id}/members/{user_id}
```

**Method:** PATCH

**Content-Type:** application/json

**Request Body:**
```json
{
  "communication_disabled_until": "2023-01-02T00:00:00.000Z"
}
```

**Response:** 204 No Content

#### Kick Member

**Endpoint:**

```
/bot/guilds/{guild_id}/members/{user_id}
```

**Method:** DELETE

**Response:** 204 No Content

#### Ban Member

**Endpoint:**

```
/bot/guilds/{guild_id}/bans/{user_id}
```

**Method:** PUT

**Content-Type:** application/json

**Request Body:**
```json
{
  "delete_message_seconds": 604800,
  "reason": "Violation"
}
```

**Response:** 204 No Content

#### Unban Member

**Endpoint:**

```
/bot/guilds/{guild_id}/bans/{user_id}
```

**Method:** DELETE

**Response:** 204 No Content

#### Edit Member

**Endpoint:**

```
/bot/guilds/{guild_id}/members/{user_id}
```

**Method:** PATCH

**Content-Type:** application/json

**Request Body:**
```json
{
  "nick": "New Nickname",
  "roles": ["role_id"],
  "mute": false,
  "deaf": false
}
```

**Response (200 OK):**
```json
{
  "user": {
    "id": "user_id",
    "username": "username"
  },
  "nick": "New Nickname",
  "roles": ["role_id"],
  "joined_at": "2023-01-01T00:00:00.000Z",
  "deaf": false,
  "mute": false
}
```

#### Add/Remove Role

**Endpoint:**

```
/bot/guilds/{guild_id}/members/{user_id}/roles/{role_id}
```

**Method:** PUT (add)

**Endpoint:**

```
/bot/guilds/{guild_id}/members/{user_id}/roles/{role_id}
```

**Method:** DELETE (remove)

**Response:** 204 No Content

#### Bulk Assign Roles

**Endpoint:**

```
/bot/guilds/{guild_id}/members/bulk-assign
```

**Method:** PATCH

**Content-Type:** application/json

**Request Body:**
```json
[
  {
    "user_id": "user_id",
    "role_ids": ["role_id_1", "role_id_2"]
  }
]
```

**Response (200 OK):**
```json
{
  "success": true
}
```

#### Bulk Remove Roles

**Endpoint:**

```
/bot/guilds/{guild_id}/members/bulk-remove
```

**Method:** PATCH

**Content-Type:** application/json

**Request Body:**
```json
[
  {
    "user_id": "user_id",
    "role_ids": ["role_id_1", "role_id_2"]
  }
]
```

**Response (200 OK):**
```json
{
  "success": true
}
```

### Roles

#### Get Roles

**Endpoint:**

```
/bot/guilds/{guild_id}/roles
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "id": "role_id",
    "name": "Role Name",
    "color": 16711680,
    "position": 1,
    "permissions": "permissions_bit",
    "mentionable": true
  }
]
```

#### Create Role

**Endpoint:**

```
/bot/guilds/{guild_id}/roles
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "name": "New Role", // optional
  "color": 16711680, // optional
  "permissions": "8" // optional
}
```

**Response (201 Created):**
```json
{
  "id": "new_role_id",
  "name": "New Role",
  "color": 16711680,
  "position": 2,
  "permissions": "8",
  "mentionable": false // optional
}
```

#### Edit Role

**Endpoint:**

```
/bot/guilds/{guild_id}/roles/{role_id}
```

**Method:** PATCH

**Content-Type:** application/json

**Request Body:**
```json
{
  "name": "Updated Role",
  "color": 65280
}
```

**Response (200 OK):**
```json
{
  "id": "role_id",
  "name": "Updated Role",
  "color": 65280,
  "position": 2,
  "permissions": "8",
  "mentionable": false
}
```

#### Delete Role

**Endpoint:**

```
/bot/guilds/{guild_id}/roles/{role_id}
```

**Method:** DELETE

**Response:** 204 No Content

### Guilds

#### Get Guild

**Endpoint:**

```
/bot/guilds/{guild_id}
```

**Method:** GET

**Response (200 OK):**
```json
{
  "id": "guild_id",
  "name": "Guild Name",
  "icon": "https://example.com/icon.png",
  "owner_id": "owner_id",
  "unavailable": false
}
```

#### Get Bans

**Endpoint:**

```
/bot/guilds/{guild_id}/bans
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "reason": "Violation",
    "user": {
      "id": "banned_user_id",
      "username": "banned_user",
      "display_name": "Banned User"
    }
  }
]
```

#### Get Ban

**Endpoint:**

```
/bot/guilds/{guild_id}/bans/{user_id}
```

**Method:** GET

**Parameters:**
- `guild_id` (string): The guild ID
- `user_id` (string): The user ID

**Response (200 OK):**
```json
{
  "reason": "Violation", // optional
  "user": {
    "id": "user_id",
    "username": "username",
    "display_name": "Display Name" // optional
  }
}
```

#### Get Emojis

**Endpoint:**

```
/bot/guilds/{guild_id}/emojis
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "id": "emoji_id",
    "name": "emoji_name",
    "animated": false
  }
]
```

#### Get Invites

**Endpoint:**

```
/bot/guilds/{guild_id}/invites
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "code": "invite_code",
    "guild": {
      "id": "guild_id",
      "name": "Guild Name"
    },
    "channel": {
      "id": "channel_id",
      "name": "channel_name",
      "type": 0
    },
    "inviter": {
      "id": "inviter_id",
      "username": "inviter_username"
    },
    "target_user": null,
    "target_user_type": null,
    "approximate_presence_count": 10,
    "approximate_member_count": 100,
    "expires_at": "2023-01-02T00:00:00.000Z",
    "uses": 5,
    "max_uses": 10,
    "max_age": 86400,
    "temporary": false,
    "created_at": "2023-01-01T00:00:00.000Z"
  }
]
```

### Invites

#### Create Invite

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/invites
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "max_age": 86400,
  "max_uses": 10
}
```

**Response (201 Created):**
```json
{
  "code": "new_invite_code",
  "guild": {
    "id": "guild_id",
    "name": "Guild Name"
  },
  "channel": {
    "id": "channel_id",
    "name": "channel_name",
    "type": 0
  },
  "inviter": {
    "id": "bot_id",
    "username": "bot_username"
  },
  "target_user": null,
  "target_user_type": null,
  "approximate_presence_count": 10,
  "approximate_member_count": 100,
  "expires_at": "2023-01-02T00:00:00.000Z",
  "uses": 0,
  "max_uses": 10,
  "max_age": 86400,
  "temporary": false,
  "created_at": "2023-01-01T00:00:00.000Z"
}
```

#### Get Invite

**Endpoint:**

```
/bot/invites/{invite_code}
```

**Method:** GET

**Response (200 OK):**
```json
{
  "code": "invite_code",
  "guild": {
    "id": "guild_id",
    "name": "Guild Name"
  },
  "channel": {
    "id": "channel_id",
    "name": "channel_name",
    "type": 0
  },
  "inviter": {
    "id": "inviter_id",
    "username": "inviter_username"
  },
  "target_user": null,
  "target_user_type": null,
  "approximate_presence_count": 10,
  "approximate_member_count": 100,
  "expires_at": "2023-01-02T00:00:00.000Z",
  "uses": 5,
  "max_uses": 10,
  "max_age": 86400,
  "temporary": false,
  "created_at": "2023-01-01T00:00:00.000Z"
}
```

#### Delete Invite

**Endpoint:**

```
/bot/invites/{invite_code}
```

**Method:** DELETE

**Response:** 204 No Content

### Users

#### Get User

**Endpoint:**

```
/bot/users/{user_id}
```

**Method:** GET

**Response (200 OK):**
```json
{
  "id": "user_id",
  "username": "username",
  "display_name": "Display Name",
  "avatar_url": "https://example.com/avatar.png",
  "bot": false
}
```

#### Get Current User

**Endpoint:**

```
/bot/users/@me
```

**Method:** GET

**Response (200 OK):**
```json
{
  "id": "user_id",
  "username": "username",
  "display_name": "Display Name",
  "avatar_url": "https://example.com/avatar.png",
  "bot": false
}
```

### Slash Commands

#### Register Global Commands

**Endpoint:**

```
/applications/{application_id}/commands
```

**Method:** PUT

**Content-Type:** application/json

**Request Body:**
```json
[
  {
    "name": "ping",
    "description": "Pong!",
    "type": 1,
    "options": []
  }
]
```

**Response (200 OK):**
```json
[
  {
    "id": "command_id",
    "application_id": "application_id",
    "name": "ping",
    "description": "Pong!",
    "type": 1,
    "options": []
  }
]
```

#### Register Guild Commands

**Endpoint:**

```
/applications/{application_id}/guilds/{guild_id}/commands
```

**Method:** PUT

**Content-Type:** application/json

**Request Body:**
```json
[
  {
    "name": "ping",
    "description": "Pong!",
    "type": 1,
    "options": []
  }
]
```

**Response (200 OK):**
```json
[
  {
    "id": "command_id",
    "application_id": "application_id",
    "name": "ping",
    "description": "Pong!",
    "type": 1,
    "options": []
  }
]
```

#### List Commands

**Global Endpoint:**

```
/applications/{application_id}/commands
```

**Guild Endpoint:**

```
/applications/{application_id}/guilds/{guild_id}/commands
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "id": "command_id",
    "application_id": "application_id",
    "name": "ping",
    "description": "Pong!",
    "type": 1,
    "options": []
  }
]
```

#### Get Command

**Global Endpoint:**

```
/applications/{application_id}/commands/{command_id}
```

**Guild Endpoint:**

```
/applications/{application_id}/guilds/{guild_id}/commands/{command_id}
```

**Method:** GET

**Response (200 OK):**
```json
{
  "id": "command_id",
  "application_id": "application_id",
  "name": "ping",
  "description": "Pong!",
  "type": 1,
  "options": []
}
```

#### Update Command

**Global Endpoint:**

```
/applications/{application_id}/commands/{command_id}
```

**Guild Endpoint:**

```
/applications/{application_id}/guilds/{guild_id}/commands/{command_id}
```

**Method:** PATCH

**Content-Type:** application/json

**Request Body:**
```json
{
  "name": "updated_ping",
  "description": "Updated Pong!"
}
```

**Response (200 OK):**
```json
{
  "id": "command_id",
  "application_id": "application_id",
  "name": "updated_ping",
  "description": "Updated Pong!",
  "type": 1,
  "options": []
}
```

#### Delete Command

**Global Endpoint:**

```
/applications/{application_id}/commands/{command_id}
```

**Guild Endpoint:**

```
/applications/{application_id}/guilds/{guild_id}/commands/{command_id}
```

**Method:** DELETE

**Response:** 204 No Content

### Webhooks

#### Get Channel Webhooks

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/webhooks
```

**Method:** GET

**Response (200 OK):**
```json
[
  {
    "id": "webhook_id",
    "type": 1,
    "guild_id": "guild_id",
    "channel_id": "channel_id",
    "user": {
      "id": "user_id",
      "username": "username"
    },
    "name": "My Webhook",
    "avatar": null,
    "token": "webhook_token"
  }
]
```

#### Create Webhook

**Endpoint:**

```
/bot/guilds/{guild_id}/channels/{channel_id}/webhooks
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "name": "My Webhook"
}
```

**Response (201 Created):**
```json
{
  "id": "webhook_id",
  "type": 1,
  "guild_id": "guild_id",
  "channel_id": "channel_id",
  "user": {
    "id": "bot_id",
    "username": "bot_username"
  },
  "name": "My Webhook",
  "avatar": null,
  "token": "webhook_token"
}
```

### Interactions

#### Create Interaction Response

**Endpoint:**

```
/interactions/{interaction_id}/{interaction_token}/callback
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "type": 4,
  "data": {
    "content": "Pong!"
  }
}
```

#### Edit Interaction Response

**Endpoint:**

```
/webhooks/{application_id}/{interaction_token}/messages/@original
```

**Method:** PATCH

**Content-Type:** application/json

**Request Body:**
```json
{
  "content": "Updated response"
}
```

**Response (200 OK):**
```json
{
  "id": "message_id",
  "channel_id": "channel_id",
  "guild_id": "guild_id",
  "author": {
    "id": "bot_id",
    "username": "bot_username",
    "bot": true
  },
  "content": "Updated response",
  "timestamp": "2023-01-01T00:00:00.000Z"
}
```

#### Delete Interaction Response

**Endpoint:**

```
/webhooks/{application_id}/{interaction_token}/messages/@original
```

**Method:** DELETE

**Response:** 204 No Content

#### Create Followup Message

**Endpoint:**

```
/webhooks/{application_id}/{interaction_token}
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "content": "Followup message"
}
```

**Response (200 OK):**
```json
{
  "id": "message_id",
  "channel_id": "channel_id",
  "guild_id": "guild_id",
  "author": {
    "id": "bot_id",
    "username": "bot_username",
    "bot": true
  },
  "content": "Followup message",
  "timestamp": "2023-01-01T00:00:00.000Z"
}
```

#### Create Thread from Message

**Endpoint:**

```
/bot/channels/{channel_id}/messages/{message_id}/threads
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "name": "Discussion",
  "auto_archive_duration": 1440
}
```

**Response (201 Created):**
```json
{
  "id": "thread_id",
  "type": 11,
  "guild_id": "guild_id",
  "name": "Discussion",
  "auto_archive_duration": 1440,
  "created_at": "2023-01-01T00:00:00.000Z"
}
```

#### Create Thread

**Endpoint:**

```
/bot/channels/{channel_id}/threads
```

**Method:** POST

**Content-Type:** application/json

**Request Body:**
```json
{
  "name": "New Thread",
  "type": 11,
  "auto_archive_duration": 1440
}
```

**Response (201 Created):**
```json
{
  "id": "thread_id",
  "type": 11,
  "guild_id": "guild_id",
  "name": "New Thread",
  "auto_archive_duration": 1440,
  "created_at": "2023-01-01T00:00:00.000Z"
}
```

#### Join/Leave Thread

**Endpoint:**

```
/bot/channels/{thread_id}/thread-members/@me
```

**Method:** PUT (join)

**Endpoint:**

```
/bot/channels/{thread_id}/thread-members/@me
```

**Method:** DELETE (leave)

**Response:** 204 No Content

## WebSocket Gateway

The WebSocket gateway provides real-time events. Connect to `wss://gateway.jubbio.com` and send an identify payload with your bot token.

### Connection

Send this payload after connecting:

```json
{
  "op": 2,
  "d": {
    "token": "YOUR_BOT_TOKEN",
    "intents": 12345,
    "properties": {
      "os": "your_os",
      "browser": "your_app",
      "device": "your_device"
    }
  }
}
```

### Gateway Intents

- `1 << 0` (Guilds): Guild events
- `1 << 1` (GuildMembers): Member events
- `1 << 2` (GuildBans): Ban events
- `1 << 3` (GuildEmojis): Emoji events
- `1 << 4` (GuildIntegrations): Integration events
- `1 << 5` (GuildWebhooks): Webhook events
- `1 << 6` (GuildInvites): Invite events
- `1 << 7` (GuildVoiceStates): Voice state events
- `1 << 8` (GuildPresences): Presence events
- `1 << 9` (GuildMessages): Message events
- `1 << 10` (GuildMessageReactions): Reaction events
- `1 << 11` (GuildMessageTyping): Typing events
- `1 << 12` (DirectMessages): DM events
- `1 << 13` (GuildScheduledEvents): Scheduled event events
- `1 << 14` (AutoModerationConfiguration): Auto moderation config events
- `1 << 15` (AutoModerationExecution): Auto moderation execution events

### Events

Events are sent with op code 0. The event name is in `t`, data in `d`.

#### Ready

Sent when the bot connects successfully.

```json
{
  "t": "READY",
  "d": {
    "v": 10,
    "user": {
      "id": "bot_id",
      "username": "bot_username",
      "display_name": "Bot Display Name",
      "avatar_url": "https://example.com/avatar.png",
      "bot": true
    },
    "guilds": [
      {
        "id": "guild_id",
        "name": "Guild Name",
        "unavailable": false
      }
    ],
    "session_id": "session_id",
    "application": {
      "id": "application_id"
    }
  }
}
```

#### Message Create

```json
{
  "t": "MESSAGE_CREATE",
  "d": {
    "id": "message_id",
    "channel_id": "channel_id",
    "guild_id": "guild_id", // optional
    "author": {
      "id": "user_id",
      "username": "username",
      "display_name": "Display Name",
      "avatar_url": "https://example.com/avatar.png",
      "bot": false
    },
    "user_id": 123, // optional
    "content": "Hello!",
    "timestamp": "2023-01-01T00:00:00.000Z",
    "edited_timestamp": null, // optional
    "attachments": [], // optional
    "embeds": [] // optional
  }
}
```

#### Interaction Create

```json
{
  "t": "INTERACTION_CREATE",
  "d": {
    "id": "interaction_id",
    "application_id": "application_id",
    "type": 2,
    "data": {
      "id": "command_id",
      "name": "ping",
      "type": 1,
      "options": []
    },
    "guild_id": "guild_id", // optional
    "channel_id": "channel_id", // optional
    "member": { // optional
      "user": {
        "id": "user_id",
        "username": "username"
      },
      "roles": ["role_id"],
      "joined_at": "2023-01-01T00:00:00.000Z"
    },
    "user": { // optional
      "id": "user_id",
      "username": "username"
    },
    "message": { // optional
      "id": "message_id",
      "content": "Message content"
    },
    "token": "interaction_token",
    "version": 1
  }
}
```

#### Guild Create

```json
{
  "t": "GUILD_CREATE",
  "d": {
    "id": "guild_id",
    "name": "Guild Name",
    "icon": "https://example.com/icon.png",
    "owner_id": "owner_id",
    "unavailable": false
  }
}
```

#### Guild Update

```json
{
  "t": "GUILD_UPDATE",
  "d": {
    "id": "guild_id",
    "name": "Updated Guild Name",
    "icon": "https://example.com/new_icon.png"
  }
}
```

#### Guild Delete

```json
{
  "t": "GUILD_DELETE",
  "d": {
    "id": "guild_id",
    "unavailable": true
  }
}
```

#### Guild Member Add

```json
{
  "t": "GUILD_MEMBER_ADD",
  "d": {
    "guild_id": "guild_id",
    "user": { // optional
      "id": "user_id",
      "username": "username"
    },
    "nick": null, // optional
    "avatar": null, // optional
    "roles": [],
    "joined_at": "2023-01-01T00:00:00.000Z",
    "deaf": false, // optional
    "mute": false // optional
  }
}
```

#### Guild Member Update

```json
{
  "t": "GUILD_MEMBER_UPDATE",
  "d": {
    "guild_id": "guild_id",
    "user": {
      "id": "user_id",
      "username": "username"
    },
    "nick": "New Nickname",
    "roles": ["new_role_id"],
    "joined_at": "2023-01-01T00:00:00.000Z"
  }
}
```

#### Guild Member Remove

```json
{
  "t": "GUILD_MEMBER_REMOVE",
  "d": {
    "guild_id": "guild_id",
    "user": {
      "id": "user_id",
      "username": "username"
    }
  }
}
```

#### Channel Create

```json
{
  "t": "CHANNEL_CREATE",
  "d": {
    "id": "channel_id",
    "type": 0,
    "guild_id": "guild_id", // optional
    "name": "new-channel", // optional
    "topic": null, // optional
    "position": 1, // optional
    "parent_id": "category_id" // optional
  }
}
```

#### Channel Update

```json
{
  "t": "CHANNEL_UPDATE",
  "d": {
    "id": "channel_id",
    "type": 0,
    "guild_id": "guild_id",
    "name": "updated-channel",
    "topic": "New topic"
  }
}
```

#### Channel Delete

```json
{
  "t": "CHANNEL_DELETE",
  "d": {
    "id": "channel_id",
    "type": 0,
    "guild_id": "guild_id",
    "name": "deleted-channel"
  }
}
```

#### Message Update

```json
{
  "t": "MESSAGE_UPDATE",
  "d": {
    "id": "message_id",
    "channel_id": "channel_id",
    "guild_id": "guild_id",
    "author": {
      "id": "user_id",
      "username": "username"
    },
    "content": "Updated content",
    "timestamp": "2023-01-01T00:00:00.000Z",
    "edited_timestamp": "2023-01-01T00:01:00.000Z"
  }
}
```

#### Message Delete

```json
{
  "t": "MESSAGE_DELETE",
  "d": {
    "id": "message_id",
    "channel_id": "channel_id",
    "guild_id": "guild_id"
  }
}
```

#### Message Delete Bulk

```json
{
  "t": "MESSAGE_DELETE_BULK",
  "d": {
    "ids": ["message_id_1", "message_id_2"],
    "channel_id": "channel_id",
    "guild_id": "guild_id"
  }
}
```

#### Role Create

```json
{
  "t": "GUILD_ROLE_CREATE",
  "d": {
    "guild_id": "guild_id",
    "role": {
      "id": "role_id",
      "name": "New Role",
      "color": 16711680,
      "position": 1,
      "permissions": "8"
    }
  }
}
```

#### Role Update

```json
{
  "t": "GUILD_ROLE_UPDATE",
  "d": {
    "guild_id": "guild_id",
    "role": {
      "id": "role_id",
      "name": "Updated Role",
      "color": 65280,
      "position": 1,
      "permissions": "8"
    }
  }
}
```

#### Role Delete

```json
{
  "t": "GUILD_ROLE_DELETE",
  "d": {
    "guild_id": "guild_id",
    "role_id": "role_id"
  }
}
```

#### Typing Start

```json
{
  "t": "TYPING_START",
  "d": {
    "channel_id": "channel_id",
    "user_id": "user_id",
    "timestamp": "2023-01-01T00:00:00.000Z"
  }
}
```

#### Error

```json
{
  "t": "ERROR",
  "d": {
    "message": "Error message",
    "code": 4004
  }
}
```

#### Debug

```json
{
  "t": "DEBUG",
  "d": {
    "message": "Debug info"
  }
}
```

#### Heartbeat

Send heartbeat every 45 seconds:

```json
{
  "op": 1,
  "d": last_sequence_number
}
```

## Voice API

Jubbio uses LiveKit for voice functionality. Voice connections are managed through the WebSocket gateway.

### Connecting to Voice

To connect to a voice channel:

1. **Send Voice State Update**: Send a WebSocket message to update voice state (join/leave channel).

**WebSocket Payload:**
```json
{
  "op": 4,
  "d": {
    "guild_id": "guild_id",
    "channel_id": "channel_id", // null to leave
    "self_mute": false,
    "self_deaf": false
  }
}
```

2. **Receive Voice Server Update**: The gateway sends voice server connection information.

**WebSocket Event:**
```json
{
  "t": "VOICE_SERVER_UPDATE",
  "d": {
    "token": "voice_token",
    "guild_id": "guild_id",
    "endpoint": "voice.livekit.jubbio.com",
    "room": "room_name"
  }
}
```

3. **Connect to LiveKit**: Use the provided token, endpoint, and room to connect to LiveKit for voice data transmission.

### Voice State Update Event

Sent when voice state changes (join, leave, mute, deaf).

**WebSocket Event:**
```json
{
  "t": "VOICE_STATE_UPDATE",
  "d": {
    "guild_id": "guild_id", // optional
    "channel_id": "channel_id", // optional
    "user_id": "user_id",
    "session_id": "session_id", // optional
    "self_mute": false,
    "self_deaf": false,
    "mute": false, // optional
    "deaf": false // optional
  }
}
```
