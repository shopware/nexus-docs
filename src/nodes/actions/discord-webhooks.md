---
nav:
  title: "Discord Webhooks"
  position: 6
---

# Discord Webhooks

Use the Discord Webhooks node to post messages to a Discord channel through an incoming webhook. It suits paid-order notifications for a fulfilment channel, failed-payment escalations, low-stock alerts and notifications when a sync fails, with rich detail in embeds.

Pick an **Operation**. **Send message** posts **Message content** and up to ten **Embeds** to the channel the webhook belongs to, optionally into a **Thread ID**. **Get message**, **Edit message** and **Delete message** work on the **Message ID** of a message sent through the same webhook. **Allowed mentions** defaults to no pings at all, so `@everyone`, role and user mentions in templated text stay silent unless you change it. For full control, **Advanced payload JSON** replaces the structured fields with a raw Discord webhook payload.

:::info
**Node type:** `action.discord-webhooks-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **discord-webhooks** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Discord, open the channel settings, go to Integrations, Webhooks, create a webhook and copy its URL. The webhook is bound to that channel.
2. In Nexus, open **Integrations** and select **Discord Webhooks**.
3. Paste the URL into **Webhook URL** and set the **Timeout (ms)** for webhook requests (10000 is a sensible default). Save.

The webhook URL contains the webhook's secret token; treat it like a password.

## Operations

- **Send message** `send-message`
- **Get message** `get-message`
- **Edit message** `edit-message`
- **Delete message** `delete-message`

## Node parameters

Configure this node with these parameters.

### Operation

Discord webhook operation to execute

- Type: Dropdown
- Required
- Default: `send-message`

Options:

- **Send message** `send-message`
- **Get message** `get-message`
- **Edit message** `edit-message`
- **Delete message** `delete-message`

### Message ID

Discord message ID

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Get message**, **Edit message**, **Delete message**

### Message content

Plain Discord message content. Use embeds for rich order, payment, stock, or workflow-failure details.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send message**, **Edit message**

### Embeds

Up to 10 Discord embed objects for rich commerce notifications. Common fields include title, description, url, color, footer, image, thumbnail, author, and fields[].

- Type: JSON
- Optional
- Shown when **Operation** is one of **Send message**, **Edit message**

### Advanced payload JSON

Advanced Discord webhook payload object. When provided, it is used instead of the structured content, embeds, username, avatar, and mention fields.

- Type: JSON
- Optional
- Shown when **Operation** is one of **Send message**, **Edit message**

### Allowed mentions

Discord allowed_mentions policy. The default parse=[] prevents accidental @everyone, role, or user pings.

- Type: JSON
- Optional
- Default: `{"parse": []}`
- Shown when **Operation** is one of **Send message**, **Edit message**

### Wait for receipt

When enabled, Discord returns the created message object for Nexus audit evidence.

- Type: Toggle
- Optional
- Default: `true`
- Shown when **Operation** is **Send message**

### Override username

Optional display name override for this webhook message.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send message**, **Edit message**

### Override avatar URL

Optional avatar URL override for this webhook message.

- Type: Text
- Optional
- Shown when **Operation** is one of **Send message**, **Edit message**

### Text to speech

Send the Discord message using text-to-speech.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Send message**, **Edit message**

### Suppress notifications

Suppress push and desktop notifications where Discord supports the flag.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Send message**, **Edit message**

### Suppress embeds

Set Discord's suppress-embeds message flag.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Send message**, **Edit message**

### Thread ID

Optional Discord thread ID.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send message**, **Get message**, **Edit message**, **Delete message**

### Thread name

Optional Discord thread name.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send message**, **Edit message**

### Applied tag IDs

Optional Discord tag IDs.

- Type: JSON
- Optional
- Shown when **Operation** is one of **Send message**, **Edit message**

### Workflow metadata

Nexus-only metadata such as scenario, Shopware IDs, and run correlation fields.

- Type: JSON
- Optional

### Timeout (ms)

Per-request timeout for Discord webhook calls. Credential-level timeout is used when this is empty.

- Type: Number
- Optional
- Default: `10000`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Discord message templates |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Discord webhook delivery receipt, message metadata, or deletion status |

**Send message**, **Edit message** and **Delete message** return a Nexus receipt with `id`, `messageId`, `channelId`, `threadId`, `timestamp`, `status`, `content` and `wait`, with Discord's own message object under `raw`. With **Wait for receipt** enabled (the default), `messageId` is filled in, so a node directly after this one can pass it to **Edit message** or **Delete message**. **Get message** returns the Discord message object.

## Examples

### Paid order notification

Send a rich order notification without allowing @everyone, role, or user pings.

```json
{
  "operation": "send-message",
  "credentialName": "discord-webhooks-mock",
  "wait": true,
  "allowedMentions": {
    "parse": []
  },
  "content": "Paid Shopware order {{orderNumber}} is ready for fulfilment.",
  "embeds": [
    {
      "title": "Paid order {{orderNumber}}",
      "color": 5763719
    }
  ]
}
```

## Related resources

- [Discord Webhook Resource](https://docs.discord.com/developers/resources/webhook)
- [Discord: Execute webhook](https://discord.com/developers/docs/resources/webhook#execute-webhook)
- [Discord: Allowed mentions](https://discord.com/developers/docs/resources/message#allowed-mentions-object)
- [Discord: Embed limits](https://discord.com/developers/docs/resources/message#embed-object-embed-limits)
- [Discord: Rate limits](https://discord.com/developers/docs/topics/rate-limits)
- [Discord: Intro to webhooks](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: Nexus stopped the request before sending it. Common causes are a **Send message** without **Message content**, **Embeds** or **Advanced payload JSON**; content over 2,000 bytes (accented letters and emoji take more than one byte); more than ten embeds; an embed with no title, description, fields or image, or with a field missing its name or value; an **Allowed mentions** value that lists the same type both under `parse` and explicitly; or an **Edit message**, **Delete message** or **Get message** without a **Message ID**. Correct the node settings and run again.
- **The requested resource was not found**: Discord does not know the webhook or the message. The webhook was deleted in Discord, or the **Message ID** was not sent through this webhook, or the message is in a thread and **Thread ID** is missing. For a deleted webhook, create a new one and update the credential under **Integrations**.
- **Authentication failed for the connected service**: Discord rejected the webhook token, usually because the **Webhook URL** in the credential is incomplete or was regenerated. Copy the URL again from the channel's webhook settings.
- **The connected service rejected the request**: Discord refused the message content, for example because an embed title is over 256 characters, the total embed text is over 6,000 characters or a field does not match the format Discord expects. Shorten the content or check the embed JSON.
- **The connected service is rate limiting requests**: Discord limits how often a webhook can post. **Send message**, **Edit message** and **Delete message** run once and are not retried, so a burst fails the step; only **Get message** waits and retries. Add a **Delay** node or combine several events into one message.
- **Mentions do not notify anyone**: expected with the default **Allowed mentions** of `{"parse": []}`. Set it to `{"parse": ["users"]}` or `{"parse": ["roles"]}` to allow those pings deliberately.
- **The message was posted to the channel instead of a thread**: set **Thread ID** to an existing thread's ID. **Thread name** only creates a new post in forum channels.
