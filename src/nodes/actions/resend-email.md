---
nav:
  title: "Resend Email"
  position: 25
---

# Resend Email

Use the Resend Email node to send a transactional email through Resend from a workflow. It suits order confirmations, shipping notifications, welcome emails and internal alerts such as a low-stock warning to an operations mailbox.

The node sends one email per record it receives. Set **From Email**, **To Email**, **Subject** and an **HTML Content** or **Text Content** body, using expressions such as <code v-pre>{{data.orderNumber}}</code> to fill in data from the trigger or the previous node. When the node receives an array of records (for example after a **Field Mapping** node fans out an order list), each record is templated separately and sent through Resend's batch endpoint in groups of up to 100. **Schedule Send** and **Attachments** are not supported by the batch endpoint, so records that use them are sent one at a time instead.

:::info
**Node type:** `action.resend-email` · **Category:** Action
:::

## Credentials

:::info
This node uses a **resend** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Resend, verify the domain you will send from (SPF and DKIM records) and create an API key with sending permission.
2. In Nexus, open **Integrations** and select **Resend**.
3. Enter a name for the integration and paste the key into **API key**. Resend keys start with `re_`.
4. Use **Test connection**, then save.

## Node parameters

Configure this node with these parameters.

### From Email

Sender address — must use a verified Resend domain

- Type: Text
- Required

### To Email

Recipient address(es), comma-separated. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required

### Subject

Email subject line. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required

### HTML Content

HTML email body. Provide html and/or text. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Optional

### Text Content

Plain-text email body (fallback when html is empty).

- Type: Code / template
- Optional

### CC

CC address(es), comma-separated.

- Type: Text
- Optional

### BCC

BCC address(es), comma-separated.

- Type: Text
- Optional

### Reply-To

Reply-To address(es), comma-separated.

- Type: Text
- Optional

### Schedule Send

Optional. Send at a future time. ISO 8601 (2026-07-01T09:00:00Z) or natural language (in 1 hour). Supports <code v-pre>{{field}}</code> templating. Empty = send now. Not available with batch (multi-record) sends.

- Type: Text
- Optional

### Attachments

Optional array of attachments, each { filename, content (base64) OR path (remote URL), contentType, contentId }. String fields support <code v-pre>{{field}}</code> templating. Not available with batch sends.

- Type: JSON
- Optional
- Default: `[]`

### Tags

Optional array of Resend tags for analytics/filtering, each { name, value }.

- Type: JSON
- Optional
- Default: `[]`

### Custom Headers

Optional custom email headers as a JSON object, e.g. { "X-Entity-Ref-ID": "<code v-pre>{{id}}</code>" }.

- Type: Code / template
- Optional

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for the email template |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Resend send result (message id in metadata) |

The node passes its input data through unchanged and adds the Resend message ID to the output metadata as `resend_email_id`.

## Examples

### Order confirmation

Connect a **Shopware Event Trigger** listening for **Order placed** to this node and configure:

| Parameter | Value |
| --- | --- |
| From Email | `orders@yourshop.com` |
| To Email | <code v-pre>{{data.orderCustomer.email}}</code> |
| Subject | <code v-pre>Thanks for your order {{data.orderNumber}}</code> |
| HTML Content | <code v-pre>&lt;p&gt;Hi {{data.orderCustomer.firstName}}, we received order {{data.orderNumber}} for {{data.amountTotal}} {{data.currency.isoCode}}.&lt;/p&gt;</code> |
| Tags | `[{"name": "type", "value": "order-confirmation"}]` |

### Low-stock alert to an operations mailbox

| Parameter | Value |
| --- | --- |
| From Email | `alerts@yourshop.com` |
| To Email | `warehouse@yourshop.com` |
| Subject | <code v-pre>Low stock: {{data.productNumber}}</code> |
| Text Content | <code v-pre>{{data.name}} ({{data.productNumber}}) is down to {{data.stock}} units.</code> |

## Related resources

- [Resend: Send email API](https://resend.com/docs/api-reference/emails/send-email)
- [Resend: Send batch emails](https://resend.com/docs/api-reference/emails/send-batch-emails)
- [Resend: Idempotency keys](https://resend.com/docs/dashboard/emails/idempotency-keys)
- [Resend: Verify a domain](https://resend.com/docs/dashboard/domains/introduction)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Permission denied by the connected service**: Resend refused the send. The two usual reasons are that the API key is not valid (Resend reports it as an invalid API key) and that the domain in **From Email** is not verified in Resend, or the address does not use a verified domain. Check the domain status in Resend and send from an address on it; if the domain is fine, create a new API key with sending access and update the credential under **Integrations**.
- **The connected service rejected the request**: Resend accepted the key but not the email. Common reasons are a **From Email** that is not a valid address or `Name <address>` pair, an invalid attachment, a **Schedule Send** value Resend cannot parse, or a required field Resend considers missing. Check the rendered values in the run's input and fix the field.
- **The connected service is rate limiting requests**: there are two different causes. Resend's per-second rate limit (5 requests per second per team, shared by all keys) is retried automatically after the wait Resend asks for, so you only see this message when the retries run out. A used-up daily or monthly sending quota is not retried; raise the quota in Resend and run the workflow again.
- **The node configuration is invalid**: a required value is empty after expressions are resolved (**From Email**, **To Email**, **Subject**, or both **HTML Content** and **Text Content**), **Attachments**, **Tags** or **Custom Headers** is not valid JSON, an attachment sets both or neither of `content` and `path`, or the selected credential is missing or of the wrong type. The details name the credential problem when there is one. An expression that points at a field the record does not have resolves to empty, which is the most common cause of a missing recipient.
- **A retried step did not send a second email**: expected. Nexus sends an idempotency key with every email, so when a failed step is retried, Resend returns the original result for 24 hours instead of sending a duplicate. Separate workflow runs always send, and so do records with different data in the same run.
