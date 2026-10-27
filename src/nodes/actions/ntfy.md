---
nav:
  title: "ntfy API"
  position: 35
---

# ntfy API

Use the ntfy API node to send push notifications to phones and desktops through ntfy from a workflow. ntfy is topic-based: the workflow publishes to a topic and every device subscribed to that topic gets the notification. It suits new-order alerts for a shop owner, low-stock warnings for a warehouse team, high-value order escalations and alerts when a sync fails.

Choose the **Topic access** first: **Public topic** publishes to ntfy.sh without a credential; **Protected topic or private server** uses an ntfy credential. Then pick an **Operation**. **Publish notification** sends a **Message** to a **Topic** with an optional **Title**, **Priority** (1 to 5), **Tags**, **Click URL**, **Delay**, up to three **Action buttons** and more. **Poll topic messages** reads the messages cached on the server for a topic since a given point.

:::info
**Node type:** `action.ntfy-api` · **Category:** Action
:::

## Credentials

:::info
A **ntfy** credential is optional for this node. The steps below explain when you need one.
:::

A credential is only needed for protected topics, private servers and email forwarding. Public topics on ntfy.sh work without one.

1. In your ntfy account settings, create an access token (or use a token created on your self-hosted server).
2. In Nexus, open **Integrations** and select **ntfy**.
3. Enter the **Server URL** (`https://ntfy.sh` or your own server) and the **Access token (optional)** (starts with `tk_`). **Default topic (optional)** can stay empty.
4. Use **Test connection**, then save.

On the node, set **Topic access** to **Protected topic or private server** and select the credential.

## Operations

- **Publish notification** `publishNotification`
- **Poll topic messages** `pollTopicMessages`

## Node parameters

Configure this node with these parameters.

### Topic access

Public topics on ntfy.sh need no credential. Choose protected for private servers or access-token protected topics.

- Type: Dropdown
- Required
- Default: `public`

Options:

- **Public topic** `public`
- **Protected topic or private server** `credentials`

### Operation

ntfy operation to perform

- Type: Dropdown
- Required
- Default: `publishNotification`

Options:

- **Publish notification** `publishNotification`
- **Poll topic messages** `pollTopicMessages`

### Topic

ntfy topic name. Treat production topic names as shared-secret-like routing keys.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)

### Message

Notification body shown in ntfy clients

- Type: Code / template
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Markdown

Render the message body as Markdown where supported

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Publish notification**

### Title

Optional notification title

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Priority

ntfy priority from 1 (minimum) to 5 (maximum)

- Type: Number
- Optional
- Default: `3`
- Shown when **Operation** is **Publish notification**

### Tags

Emoji or tag names shown by ntfy clients

- Type: JSON
- Optional
- Default: `[]`
- Shown when **Operation** is **Publish notification**

### Click URL

URL opened when the notification is clicked

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Icon URL

Optional notification icon URL

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Attachment URL

Optional external attachment URL

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Attachment filename

Optional filename displayed for the attachment

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Delay

Optional scheduled delivery such as 30m, 9am, or an ISO timestamp

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Email

Optional email forwarding address if the ntfy server allows it

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification** and **Topic access** is **Protected topic or private server**

### Cache

Keep enabled so validation can poll the message back from ntfy cache

- Type: Toggle
- Optional
- Default: `true`
- Shown when **Operation** is **Publish notification**

### Firebase push

Forward to mobile push where the ntfy server supports it

- Type: Toggle
- Optional
- Default: `true`
- Shown when **Operation** is **Publish notification**

### Action buttons

Up to three ntfy action button objects

- Type: JSON
- Optional
- Default: `[]`
- Shown when **Operation** is **Publish notification**

### Sequence ID

Optional sequence identifier for visible notification replacement on compatible clients

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Publish notification**

### Since

Message ID, Unix timestamp, duration, or all for cached poll read-back

- Type: Text
- Optional
- Default: `all`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Poll topic messages**

### Include scheduled

Include delayed messages where supported by the ntfy server

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Poll topic messages**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Shopware event or mapped notification payload |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | ntfy publish response including messageId |

**Publish notification** returns ntfy's response for the published message, including its `id`, `topic`, `time` and `event`. **Poll topic messages** returns a list of cached messages, and the node after this one runs once per message.

## Examples

### Push a new-order alert to the shop owner's phone

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`) to this node and configure:

| Parameter | Value |
| --- | --- |
| Topic access | Protected topic or private server |
| Operation | Publish notification |
| Topic | `yourshop-orders-7f3a9c` |
| Title | <code v-pre>New order {{data.orderNumber}}</code> |
| Message | <code v-pre>{{data.orderCustomer.firstName}} {{data.orderCustomer.lastName}} ordered for {{data.amountTotal}} {{data.currency.isoCode}}.</code> |
| Priority | `4` |
| Tags | `["shopping_cart", "package"]` |
| Click URL | <code v-pre>https://yourshop.com/admin#/sw/order/detail/{{data.id}}</code> |

## Related resources

- [ntfy: Publishing](https://docs.ntfy.sh/publish/)
- [ntfy: Action buttons](https://docs.ntfy.sh/publish/#action-buttons)
- [ntfy: Polling for messages](https://docs.ntfy.sh/subscribe/api/#poll-for-messages)
- [ntfy: Access tokens](https://docs.ntfy.sh/config/#access-tokens)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: the step stopped before anything was sent. Either **Topic access** is set to **Protected topic or private server** but no credential is selected, or the notification breaks one of ntfy's rules, such as an empty **Message**, a **Priority** outside 1 to 5, more than three **Action buttons**, an action button without a label, or an invalid **Sequence ID**. The run details name the field and the rule. Correct it, or switch to **Public topic** if the topic needs no credential.
- **Authentication failed for the connected service**: the server did not accept the access token. Check the **Access token (optional)** in the credential and that it belongs to the server in **Server URL**.
- **Permission denied by the connected service**: the token is valid but is not allowed to publish to, or read from, that topic. Check the token's topic permissions on the server.
- **The connected service is rate limiting requests**: ntfy.sh limits requests per visitor and per account. **Publish notification** runs once and is not retried, so a burst fails the step; **Poll topic messages** waits as long as ntfy asks and tries again. For high volume, self-host ntfy or raise the account limit.
- **Anyone can read or post to my topic**: on a public server a topic name is the only protection. Use an unguessable topic name, or a protected topic on an authenticated server.
- **Poll topic messages returns nothing**: the poll reads the server's cache, which keeps messages only for a limited time, and only if **Cache** was enabled when they were published.
- **A notification arrived twice**: running the workflow again publishes the notification again. Set a **Sequence ID** so compatible clients replace the earlier notification instead of showing a second one.
