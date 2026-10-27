---
nav:
  title: "Pushover"
  position: 23
---

# Pushover

Use the Pushover node to send an operational push notification to a person's or a team's phones and desktops through Pushover. Typical Shopware cases are alerting the warehouse when an order is paid, escalating a high-value order, or warning someone when stock runs low or a payment fails.

The node has one operation, **Send message**. Set the **Message**, optionally a **Title**, a **Supplementary URL** that opens from the notification, and a **Priority**. Emergency priority repeats the notification until someone acknowledges it or it expires. A successful run means Pushover accepted and queued the message, not that someone has seen it on a device.

:::info
**Node type:** `action.pushover-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **pushover** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **Pushover**.
2. Enter a name for the connection and the **User or group key**. This is the 30-character user key shown in your Pushover dashboard, or a Pushover group key to notify several people. Treat it as private.
3. Optionally enter a **Device (optional)** name. Leave it empty to notify every active device for the key.
4. Choose **Test connection**. Nexus checks the key and device with Pushover without sending a notification, then save.

You do not need a Pushover application token. Nexus sends notifications through its own registered Pushover application.

## Operations

- **Send message** `sendMessage`

## Node parameters

Configure this node with these parameters.

### Operation

Pushover API operation to perform. This connector currently supports sending messages only.

- Type: Dropdown
- Required
- Default: `sendMessage`

Options:

- **Send message** `sendMessage`

### Message

Notification body sent to the recipient's Pushover devices

- Type: Code / template
- Required
- Placeholder: <code v-pre>Paid order {{orderNumber}} is ready to fulfil.</code>
- Supports expressions (<code v-pre>{{ }}</code>)

### Title

Optional notification title

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)

### Device override

Optional active device name. Leave empty to use the connection default or all active devices.

- Type: Text
- Optional
- Validated against pattern `^[A-Za-z0-9_-]+$`

### Supplementary URL

URL opened from the notification

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)

### URL title

Label displayed for the supplementary URL

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)

### Priority

Delivery priority. Emergency messages repeat until acknowledged or expired.

- Type: Dropdown
- Required
- Default: `0`

Options:

- **Lowest (-2)** `-2`
- **Low (-1)** `-1`
- **Normal (0)** `0`
- **High (1)** `1`
- **Emergency (2)** `2`

### Sound

Optional Pushover sound name. Leave empty to use the recipient's default.

- Type: Text
- Optional
- Placeholder: `pushover`

### Time to live (seconds)

Discard the notification after this many seconds if it has not been delivered. Ignored for emergency priority.

- Type: Number
- Optional

### Enable HTML

Render supported Pushover HTML tags in the message

- Type: Toggle
- Optional
- Default: `false`

### Use monospace

Render the whole message in monospace. Cannot be combined with HTML.

- Type: Toggle
- Optional
- Default: `false`

### Emergency retry interval (seconds)

How often Pushover repeats an emergency notification until it is acknowledged

- Type: Number
- Required
- Default: `60`
- Shown when **Priority** is **Emergency (2)**

### Emergency expiry (seconds)

Stop repeating an unacknowledged emergency notification after this time

- Type: Number
- Required
- Default: `3600`
- Shown when **Priority** is **Emergency (2)**

### Attachment (base64)

Optional base64-encoded attachment. Nexus's workflow payload ceiling may be lower than Pushover's 5 MiB attachment limit.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)

### Attachment filename

Filename sent with the attachment

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Order, product, workflow, or mapped notification data |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Acceptance receipt | `object` | Safe Pushover acceptance receipt with request ID, optional emergency receipt, and quota metadata |

The output is an acceptance receipt: Pushover's request ID, the emergency receipt when the priority is emergency, and the remaining message quota. It does not contain the message text.

## Examples

### Paid order alert

Notify operations when a Shopware order is paid and ready to fulfil.

```json
{
  "operation": "sendMessage",
  "title": "Paid order",
  "message": "Order {{orderNumber}} is ready to fulfil.",
  "priority": 0,
  "url": "{{shopUrl}}/admin#/sw/order/detail/{{id}}",
  "urlTitle": "Open order"
}
```

### Alert operations about a high-value order

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`) to an **If** node that checks <code v-pre>{{amountTotal}}</code> — **>=** — `1000`, and connect its **True** port to this node:

| Parameter | Value |
| --- | --- |
| Operation | Send message |
| Title | <code v-pre>High-value order {{orderNumber}}</code> |
| Message | <code v-pre>Order {{orderNumber}} for {{amountTotal}} EUR needs a manual check.</code> |
| Priority | High (1) |
| Supplementary URL | <code v-pre>{{shopUrl}}/admin#/sw/order/detail/{{id}}</code> |
| URL title | `Open order` |

High priority can bypass the recipient's quiet hours, so keep it for alerts that justify prompt attention.

## Related resources

- [Pushover Message API](https://pushover.net/api#messages)
- [Pushover Priority and Emergency Receipts](https://pushover.net/api#priority)
- [Pushover: user and group keys](https://pushover.net/api#identifiers)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: Nexus checked the notification before sending and it breaks one of Pushover's rules. Common causes are an empty **Message** or one over 1,024 characters after the expressions are filled in, a **Title** over 250 characters, a **Supplementary URL** over 512 characters or a **URL title** over 100, **Enable HTML** and **Use monospace** both turned on, or an emergency priority without an **Emergency retry interval (seconds)** between 30 and 86,400 and an **Emergency expiry (seconds)** between 1 and 10,800. A **Device override** may only use 1 to 25 letters, numbers, underscores or hyphens.
- **The connected service rejected the request**: Pushover refused the notification. Typical reasons are a user or group key that is wrong or belongs to a disabled account or group, a key with no active devices, or a **Device override** that is not a device on that key. Copy the key again from Pushover, sign in to a Pushover app to register a device, or clear **Device override** to notify every active device.
- **The connected service is rate limiting requests**: the Pushover message allowance is used up. This is not retried; wait for the allowance to reset.
- **The connected service is currently unavailable**: Pushover returned a server error. Nexus tries the request up to three times, a few seconds apart, before the step fails; run the workflow again later.
- **The run succeeds but nobody is alerted**: a successful run only means Pushover accepted and queued the message. Check that the recipient has an active device, and note that the **Lowest (-2)** and **Low (-1)** priorities do not sound an alert.
