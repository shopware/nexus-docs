---
nav:
  title: "Postmark API"
  position: 22
---

# Postmark API

Use the Postmark API node to send transactional email through Postmark and to read delivery and bounce data back into a workflow. Typical uses are an order confirmation from a Postmark template, a plain-text alert to an internal mailbox for high-value orders, and a scheduled bounce poll that opts bounced addresses out of the Shopware newsletter.

Pick an **Operation**. **Send template email** sends a Postmark template by **Template Alias** or **Template ID** with a **Template Model**; **Send email** sends a raw **Subject** with an **HTML Body** or **Text Body**. Both take **To**, an optional single **Tag** and **Metadata**. **From** and **Message Stream** are overrides: leave **From** empty to send from the credential's **Default sender address**, and leave **Message Stream** empty to send on Postmark's default transactional stream, `outbound`. Any sender must have a confirmed sender signature or a verified domain in Postmark, and the stream must be one that can send (inbound streams cannot). When the node's **Payload** is empty and the previous node emitted a JSON object, that object is used as the request body, so a **Field Mapping** node can shape the whole email; explicit fields on the node override matching keys. The read operations fetch one message or bounce, or list outbound messages, templates and bounces with filters.

:::info
**Node type:** `action.postmark-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **postmark** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Postmark, open Servers, your server, API Tokens and copy the server token. A server token is scoped to one Postmark server; multi-server setups need one credential per server.
2. Confirm a sender signature or verify the sending domain for the address you will send from.
3. In Nexus, open **Integrations** and select **Postmark**.
4. Enter the **Server token** and, optionally, a **Default sender address** used when a workflow does not set **From**. Save.

## Operations

- **Send template email** `sendTemplateEmail`
- **Send email** `sendEmail`
- **Get message details** `getMessageDetails`
- **List outbound messages** `listOutboundMessages`
- **List templates** `listTemplates`
- **List bounces** `listBounces`
- **Get bounce** `getBounce`

## Node parameters

Configure this node with these parameters.

### Operation

Postmark operation to execute.

- Type: Dropdown
- Required
- Default: `sendTemplateEmail`

Options:

- **Send template email** `sendTemplateEmail`
- **Send email** `sendEmail`
- **Get message details** `getMessageDetails`
- **List outbound messages** `listOutboundMessages`
- **List templates** `listTemplates`
- **List bounces** `listBounces`
- **Get bounce** `getBounce`

### Payload

Complete Postmark JSON payload for /email or /email/withTemplate. Explicit fields below override matching payload keys.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**

### From

Sender override for this node. Leave empty to send from the sender email saved in the selected Postmark credential.

Sender override for this node. Leave empty to send from the sender email saved in the selected Postmark credential. Any sender must match a confirmed Postmark sender signature or verified domain.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**

### To

Recipient email address or comma-separated recipient list.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**

### Cc

Optional comma-separated CC recipient list.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**

### Bcc

Optional comma-separated BCC recipient list.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**

### Subject

Subject line for raw email sends.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email**

### HTML Body

HTML body for raw email sends.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email**

### Text Body

Plain-text body for raw email sends.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email**

### Template Alias

Postmark template alias used for template email sends.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send template email**

### Template ID

Numeric Postmark template ID. Use only when not using Template Alias.

- Type: Number
- Optional
- Shown when **Operation** is **Send template email**

### Template Model (JSON)

JSON model passed to the selected Postmark template. Use @ to insert Shopware values.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send template email**

### Tag

Postmark tag for reconciliation, search, bounce filtering, and workflow-level reporting.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**, **List outbound messages**, **List bounces**

### Metadata

String key-value metadata. Include nexus_correlation_id and Shopware identifiers for delivery and bounce reconciliation.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**

### Message Stream

Message stream override for this node. Leave empty to use the stream saved in the selected Postmark credential, or outbound when none is saved.

Message stream override for this node. Leave empty to use the stream saved in the selected Postmark credential, or outbound when none is saved. Enter a send-capable stream ID: outbound, broadcasts, or a custom transactional or broadcast stream from your Postmark server. Inbound streams cannot send. Postmark keeps transactional and bulk sending on separate streams so that bulk reputation cannot damage receipts and password resets.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Send template email**, **List outbound messages**

### Track Opens

Enable Postmark open tracking. Leave disabled unless the merchant explicitly opts in.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Send email**, **Send template email**

### Track Links

Postmark link-tracking mode. Keep None unless the merchant explicitly opts in.

- Type: Dropdown
- Optional
- Default: `None`
- Shown when **Operation** is one of **Send email**, **Send template email**

Options:

- **None** `None`
- **HTML and text** `HtmlAndText`
- **HTML only** `HtmlOnly`
- **Text only** `TextOnly`

### Message ID

Postmark MessageID for message-detail reads or bounce filtering.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Get message details**

### Message ID Filter

Filter bounces by the originating Postmark MessageID. Leave empty to list across all messages.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List bounces**

### Bounce ID

Postmark bounce ID to read in full, usually taken from a preceding List bounces row.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Get bounce**

### Recipient Filter

Filter outbound messages by recipient email address.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List outbound messages**

### From Email Filter

Filter outbound messages by sender email address.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List outbound messages**

### Status Filter

Filter outbound messages by Postmark delivery status.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List outbound messages**

### Count

Page size for list operations. Postmark caps count at 500.

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is one of **List outbound messages**, **List templates**, **List bounces**

### Offset

Pagination offset for list operations. Narrow filters before approaching Postmark's 10,000-record search window.

- Type: Number
- Optional
- Default: `0`
- Shown when **Operation** is one of **List outbound messages**, **List templates**, **List bounces**

### Total Limit

Optional total row limit across paginated read operations.

- Type: Number
- Optional
- Shown when **Operation** is one of **List outbound messages**, **List templates**, **List bounces**

### Maximum Pages

Optional safety cap for paginated readback operations.

- Type: Number
- Optional
- Shown when **Operation** is one of **List outbound messages**, **List templates**, **List bounces**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Shopware event, mapped email payload, or Postmark readback context. |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Postmark response containing MessageID, SubmittedAt, ErrorCode, Message, Tag, Metadata, MessageStream, or readback rows. |

Send operations return Postmark's response with `MessageID`, `SubmittedAt`, `ErrorCode`, `Message`, `Tag`, `Metadata` and `MessageStream`. A response with a non-zero `ErrorCode` is treated as a failure. **Get message details** and **Get bounce** return one record with its event history; the list operations return the matching rows.

## Examples

### Send order-confirmation template

Send a Postmark template email with disabled tracking and workflow correlation metadata.

```json
{
  "operation": "sendTemplateEmail",
  "TemplateAlias": "order-confirmation",
  "To": "{{orderCustomer.email}}",
  "Tag": "shopware-order-confirmation",
  "MessageStream": "outbound",
  "TrackOpens": false,
  "TrackLinks": "None",
  "Metadata": {
    "shopware_order_id": "{{data.orderId}}"
  }
}
```

### Opt bounced addresses out of the newsletter

Connect a **Schedule Trigger** to this node, then map each row to a Shopware newsletter recipient update:

| Parameter | Value |
| --- | --- |
| Operation | List bounces |
| Tag | `shopware-order-confirmation` |
| Count | `100` |
| Maximum Pages | `5` |

Bounce rows use Postmark's PascalCase field names, for example `Email`, `Type` and `BouncedAt`.

## Related resources

- [Postmark API documentation](https://postmarkapp.com/developer/api/overview)
- [Postmark: Email API](https://postmarkapp.com/developer/api/email-api)
- [Postmark: Send with template](https://postmarkapp.com/developer/api/templates-api)
- [Postmark: Bounce API](https://postmarkapp.com/developer/api/bounce-api)
- [Postmark: Messages API](https://postmarkapp.com/developer/api/messages-api)
- [Postmark: API error codes](https://postmarkapp.com/developer/api/overview#error-codes)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Postmark has not confirmed the From address. Confirm the sender signature, or verify the sending domain, in Postmark.**: the sender in **From**, or the credential's **Default sender address** when **From** is empty, has no confirmed sender signature and its domain is not verified on this Postmark server. Confirm the signature or verify the domain in Postmark, then run the workflow again.
- **Postmark has suppressed this recipient after an earlier bounce or spam complaint. Reactivate the address in Postmark to send to it again.**: Postmark marked the address inactive after a hard bounce or spam complaint. Reactivate it in Postmark only if you are sure the address is valid and the recipient wants the email.
- **Postmark is not allowing this server to send. Check that the server is approved and out of its sandbox.**: Postmark refused to let this server send. Check in your Postmark account that the account is approved and able to send, and that the server is not in test mode.
- **Postmark rejected the message (Postmark error n)**: Postmark refused the request with another of its error codes, shown as the number. Look it up in Postmark's API error code list. Common ones: 300 (invalid email request, for example a malformed **To** address), 412 or 413 (the account is still pending approval; until then Postmark only sends to recipients on the From address's domain, or not at all), 1101 (the **Template Alias** or **Template ID** does not exist on this server; enter the alias exactly as it appears on a Standard template in Postmark) and 1235 (the **Message Stream** does not exist on this server). Read operations report a Postmark refusal the same way, for example 701 when **Get message details** is given a message ID Postmark does not know and 1001 for an unknown bounce in **Get bounce**.
- **The node configuration is invalid**: Nexus stopped the step before calling Postmark. Common causes: no sender at all (neither **From** nor the credential's **Default sender address** is set), an empty **To**, **Send email** without **HTML Body** or **Text Body**, or **Send template email** without a **Template Alias** or **Template ID**, or without a **Template Model (JSON)**.
- **Authentication failed for the connected service**: the server token is wrong or was revoked. A server token belongs to one Postmark server, so templates, streams and messages must be on that same server. Copy the token again from Postmark and update the credential under **Integrations**.
- **A send failed and was not retried**: sends run at most once because Postmark has no idempotency key, so a timeout, a rate limit refusal or a Postmark outage fails the step immediately (with **The connected service did not respond in time**, **The connected service is rate limiting requests** or **The connected service is currently unavailable**). Check **List outbound messages** for the recipient before running the workflow again. Reads are retried automatically.
