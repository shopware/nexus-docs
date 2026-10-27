---
nav:
  title: "Telegram Bot"
  position: 30
---

# Telegram Bot

Use the Telegram Bot node to post operational alerts from a workflow to a Telegram chat, group, channel or forum topic through a bot you own. It suits paid-order alerts for a fulfilment team, low-stock warnings, daily digests and notifications when a sync to an ERP or marketing tool fails.

The node supports one operation, **Send message**. Set the **Chat ID** (a numeric chat ID or an `@channel` username), the **Message text** and a **Parse mode**; HTML is the default and the easiest for simple formatting. Optional fields post into a forum topic (**Topic thread ID**), send silently, protect the content, add an inline keyboard (**Reply markup JSON**) or control link previews.

:::info
**Node type:** `action.telegram-bot-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **telegram-bot** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Telegram, open BotFather, create a bot with `/newbot` and copy the token it gives you.
2. Add the bot to the destination chat or group. For a channel, make the bot an administrator.
3. In Nexus, open **Integrations** and select **Telegram Bot**.
4. Enter the **Bot token**, use **Test connection**, then save.

To find a group's numeric chat ID, send a message in the group and read the `chat.id` from the bot's updates, or use the negative ID shown by a chat-ID helper bot. Group and channel IDs are negative numbers such as `-1004242424242`.

## Operations

- **Send message** `sendMessage`

## Node parameters

Configure this node with these parameters.

### Operation

Telegram Bot API method to execute. This connector currently supports sendMessage only.

Use a BotFather bot token in the node credentials; the token is sent in Telegram's /bot{'{'}token{'}'}/sendMessage URL path and is redacted from diagnostics.

- Type: Dropdown
- Required
- Default: `sendMessage`

Options:

- **Send message** `sendMessage`

### Chat ID

Telegram chat_id, @channel username, group id, or private chat id. Keep this per action for routing.

Add the bot to the destination chat or channel first. For channels, make it an administrator; for groups, use the negative numeric chat ID when possible.

- Type: Text
- Required
- Placeholder: `-1004242424242 or @merchant_ops`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send message**

### Message text

Message body sent through sendMessage. Telegram caps text at 4,096 characters after entity parsing.

HTML parse mode is easiest for merchant alerts. MarkdownV2 requires escaping punctuation such as _, *, [, ], (, ), ~, `, >, #, +, -, =, {'|'}, {'{'}, {'}'}, ., and !.

- Type: Code / template
- Required
- Placeholder: <code v-pre>&lt;b&gt;Paid order&lt;/b&gt; {{orderNumber}} is ready to fulfil.</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send message**

### Parse mode

Optional Telegram parse_mode. Prefer HTML for simple preset templates.

Choose None for plain text. HTML supports simple tags such as &lt;b&gt;, &lt;i&gt;, &lt;code&gt;, and links; malformed markup is rejected by Telegram.

- Type: Dropdown
- Optional
- Default: `HTML`
- Shown when **Operation** is **Send message**

Options:

- **None** ``
- **HTML** `HTML`
- **MarkdownV2** `MarkdownV2`
- **Markdown** `Markdown`

### Topic thread ID

Optional Telegram forum topic message_thread_id for group destinations.

Only set this when posting into a Telegram forum topic. Leave empty for normal private, group, or channel messages.

- Type: Text
- Optional
- Placeholder: `12345`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send message**

### Send silently

Deliver without push notification sound.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Send message**

### Protect content

Prevent forwarding and saving where Telegram supports protect_content.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Send message**

### Reply markup JSON

Optional raw Telegram reply_markup JSON such as an inline keyboard button.

Advanced field: paste Telegram-compatible JSON. Presets use this for inline keyboard links to Shopware admin records.

- Type: JSON
- Optional
- Placeholder: `{"inline_keyboard":[[{"text":"Open order","url":"https://example.test/admin/order"}]]}`
- Shown when **Operation** is **Send message**

### Link preview options JSON

Optional raw Telegram link_preview_options JSON for controlling URL previews.

Advanced field: use this to disable or tune previews when messages include order, product, or incident URLs.

- Type: JSON
- Optional
- Placeholder: `{"is_disabled":true}`
- Shown when **Operation** is **Send message**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Order, product, workflow, or mapped alert data for message templates |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Delivery receipt | `object` | Telegram sendMessage delivery receipt with messageId, chatId, date, and redacted raw envelope |

The output is a delivery receipt with the Telegram `messageId`, `chatId`, `date` and `text` of the sent message, the HTTP `status`, and Telegram's full response under `raw`.

## Examples

### Paid order alert

Notify an operations chat when a Shopware order is paid and ready for fulfilment.

```json
{
  "operation": "sendMessage",
  "chatId": "-1004242424242",
  "text": "<b>Paid order</b> {{orderNumber}} is ready to fulfil.",
  "parseMode": "HTML",
  "disableNotification": false,
  "protectContent": false
}
```

### Alert an operations topic about new orders

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`) to this node and configure:

| Parameter | Value |
| --- | --- |
| Operation | Send message |
| Chat ID | `-1004242424242` |
| Topic thread ID | `12345` |
| Message text | <code v-pre>&lt;b&gt;New order&lt;/b&gt; {{orderNumber}} for {{amountTotal}} EUR is ready to fulfil.</code> |
| Parse mode | HTML |
| Link preview options JSON | `{"is_disabled": true}` |

## Related resources

- [Telegram Bot API sendMessage](https://core.telegram.org/bots/api#sendmessage)
- [BotFather](https://core.telegram.org/bots/features#botfather)
- [Telegram Bot API: formatting options](https://core.telegram.org/bots/api#formatting-options)
- [Telegram Bot FAQ: rate limits](https://core.telegram.org/bots/faq#my-bot-is-hitting-limits-how-do-i-avoid-this)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Telegram could not find the chat. Check the chat ID and that the bot is a member of that chat.**: the **Chat ID** is wrong, or the bot has not been added to the chat. Use the negative numeric ID for groups, supergroups and channels, or the `@channel` username for a public channel.
- **Telegram refused the message. Add the bot to the chat and allow it to post there; in channels it must be an administrator.**: the bot was removed from the group, was never allowed to post, is not an administrator of the channel, or the recipient blocked it. Re-add the bot with posting rights.
- **Telegram did not recognise the bot token. Check the token issued by BotFather.**: the **Bot token** in the credential is wrong or incomplete. Copy it again from BotFather and update the credential under **Integrations**. If Telegram answers that the token is unauthorised instead, for example after the token was revoked in BotFather, Nexus shows **Authentication failed for the connected service**; the fix is the same.
- **The connected service rejected the request**: Telegram refused the message content, most often because the markup is invalid for the chosen **Parse mode** (Telegram reports that it cannot parse the entities), or because **Reply markup JSON** or **Link preview options JSON** does not match what Telegram expects. In HTML, close every tag and escape `<`, `>` and `&` in data; in MarkdownV2, escape punctuation such as `.`, `-`, `(` and `!`. Set **Parse mode** to None for plain text.
- **The node configuration is invalid**: the step stopped before sending. Common causes are an empty **Chat ID** or **Message text**, or a message longer than 4,096 bytes after the expressions are filled in. The limit is counted in bytes, so text with umlauts, emoji or non-Latin scripts reaches it sooner than 4,096 characters. Shorten the template or split the content.
- **The connected service is rate limiting requests**: Telegram allows roughly one message per second to a single chat, about 20 messages per minute to a group and about 30 messages per second overall per bot. Sending is not retried, so a burst fails the step. Add a **Delay** node or combine several events into one message.
- **The message lands in the group's general chat instead of a topic**: set **Topic thread ID** to the topic's `message_thread_id`. Leave it empty for chats without forum topics.
