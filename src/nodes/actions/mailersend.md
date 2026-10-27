---
nav:
  title: "MailerSend API"
  position: 13
---

# MailerSend API

Use the MailerSend API node to send transactional email through MailerSend and to read delivery data back into a workflow. Typical uses are an order confirmation sent from a MailerSend template, a shipping notification, a suppression entry when a customer unsubscribes in Shopware, and a scheduled read of delivery activity or bounces.

Pick an **Operation**. The send operations (**Send email**, **Send template email**, **Send bulk email**) take a MailerSend-shaped JSON **Payload** or **Bulk Emails** array. The read operations list templates, domains, messages, recipients, suppressions, activity and webhooks, or fetch one record by **ID**. **List activity**, **List webhooks** and **Create webhook** are scoped to a **Domain ID**.

:::info
**Node type:** `action.mailersend-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **mailersend** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In MailerSend, verify your sending domain and create an API token with the permissions the workflow needs.
2. In Nexus, open **Integrations** and select **MailerSend**.
3. Enter the token in **API token**. Leave **Base URL** blank to use the default MailerSend API URL.
4. Fill in **Default from email**, **Default from name** and **Default domain ID** (the domain ID from your MailerSend domain settings). Workflow templates use these as sender defaults. **Webhook signing secret** is only needed for workflows that receive MailerSend webhooks.
5. Use **Test connection**, then save.

## Operations

- **Send email** `send_email`
- **Send template email** `send_template_email`
- **Send bulk email** `send_bulk_email`
- **Get bulk email** `get_bulk_email`
- **List templates** `list_templates`
- **List domains** `list_domains`
- **List messages** `list_messages`
- **Get message** `get_message`
- **List recipients** `list_recipients`
- **List suppressions** `list_suppressions`
- **Create suppression** `create_suppression`
- **Delete suppression** `delete_suppression`
- **List activity** `list_activity`
- **Get activity** `get_activity`
- **List webhooks** `list_webhooks`
- **Create webhook** `create_webhook`

## Node parameters

Configure this node with these parameters.

### Operation

MailerSend operation to execute

- Type: Dropdown
- Required
- Default: `send_template_email`

Options:

- **Send email** `send_email`
- **Send template email** `send_template_email`
- **Send bulk email** `send_bulk_email`
- **Get bulk email** `get_bulk_email`
- **List templates** `list_templates`
- **List domains** `list_domains`
- **List messages** `list_messages`
- **Get message** `get_message`
- **List recipients** `list_recipients`
- **List suppressions** `list_suppressions`
- **Create suppression** `create_suppression`
- **Delete suppression** `delete_suppression`
- **List activity** `list_activity`
- **Get activity** `get_activity`
- **List webhooks** `list_webhooks`
- **Create webhook** `create_webhook`

### Domain ID

MailerSend domain ID for domain, webhook, and activity operations scoped to a single domain.

- Type: Text
- Required
- Shown when **Operation** is one of **List webhooks**, **Create webhook**, **List activity**

### Suppression Type

Suppression list type for compliance feedback workflows.

- Type: Dropdown
- Optional
- Default: `unsubscribes`
- Shown when **Operation** is one of **List suppressions**, **Create suppression**, **Delete suppression**

Options:

- **Unsubscribes** `unsubscribes`
- **Hard bounces** `hard-bounces`
- **Spam complaints** `spam-complaints`
- **Blocklist** `blocklist`

### Activity Event

Optional MailerSend activity event filter.

- Type: Dropdown
- Optional
- Shown when **Operation** is one of **List activity**, **Get activity**

Options:

- **Sent** `activity.sent`
- **Delivered** `activity.delivered`
- **Soft bounced** `activity.soft_bounced`
- **Hard bounced** `activity.hard_bounced`
- **Deferred** `activity.deferred`
- **Unsubscribed** `activity.unsubscribed`
- **Spam complaint** `activity.spam_complaint`

### Payload

JSON payload for send and mutation operations. Use @ to insert prior-node data.

- Type: JSON
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send template email**, **Send email**, **Create webhook**, **Create suppression**, **Delete suppression**

### Bulk Emails

Array of MailerSend email payloads for send_bulk_email. Use @ to insert mapped rows.

- Type: JSON
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send bulk email**

### ID

Operation-specific identifier such as messageId, bulkEmailId, activityId, webhookId, templateId, or recipientId.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Get bulk email**, **Get message**, **Get activity**

### Limit

Maximum records to read from list endpoints.

- Type: Number
- Optional
- Default: `25`
- Shown when **Operation** is one of **List activity**, **List suppressions**, **List templates**, **List domains**, **List messages**

### Page Size

MailerSend per-page size for paginated list operations.

- Type: Number
- Optional
- Default: `25`
- Shown when **Operation** is one of **List activity**, **List suppressions**, **List templates**, **List domains**, **List messages**

### Maximum Pages

Safety cap for paginated MailerSend reads.

- Type: Number
- Optional
- Default: `3`
- Shown when **Operation** is one of **List activity**, **List suppressions**, **List templates**, **List domains**, **List messages**

### Return Record

Fetch the created MailerSend record after write operations when the API returns only headers.

- Type: Toggle
- Optional
- Default: `true`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Payload or query context for the MailerSend operation |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | MailerSend operation result and correlation metadata |

Send operations return MailerSend's response and lift the identifiers into the output metadata as `mailersend_message_id`, `mailersend_id` or `mailersend_bulk_email_id`. With **Return Record** enabled, the node fetches the created record when MailerSend returns only headers. List operations return the records under `data` with `count`, `page`, `pageSize`, `total`, `hasMore` and `nextPage`.

## Examples

### Order confirmation from a MailerSend template

Connect a **Shopware Event Trigger** listening for **Order placed** to this node and configure:

| Parameter | Value |
| --- | --- |
| Operation | Send template email |
| Payload | <code v-pre>{"template_id": "your-template-id", "from": {"email": "orders@yourshop.com", "name": "Your Shop"}, "to": [{"email": "{{data.orderCustomer.email}}"}], "personalization": [{"email": "{{data.orderCustomer.email}}", "data": {"order_number": "{{data.orderNumber}}", "order_total": "{{data.amountTotal}}"}}]}</code> |

### Read the last 30 days of hard bounces for a domain

| Parameter | Value |
| --- | --- |
| Operation | List activity |
| Domain ID | `dom_xxxxxxxxx` |
| Activity Event | Hard bounced |
| Page Size | `100` |
| Maximum Pages | `5` |

Without an explicit date range, the node reads the last 30 days.

## Related resources

- [MailerSend API reference](https://developers.mailersend.com/)
- [MailerSend: Email API](https://developers.mailersend.com/api/v1/email.html)
- [MailerSend: Activity](https://developers.mailersend.com/api/v1/activity.html)
- [MailerSend: Suppressions](https://developers.mailersend.com/api/v1/recipients.html)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: Nexus stopped the step before calling MailerSend. Common causes: **Bulk Emails** is not a plain JSON array of email objects (do not wrap it in `{"emails": [...]}`; the details name the expected type), a send **Payload** has no `to`, no `subject` or no body (`html`, `text` or `template_id`), **Delete suppression** has no suppression IDs (MailerSend deletes entries by ID, not by email address; get the IDs from **List suppressions** and pass `{"ids": ["..."]}`), or a **List activity** date range is longer than 30 days or not given as Unix timestamps in UTC.
- **The connected service rejected the request**: MailerSend refused the content of the request. For sends this usually means the `from` address is not on a domain verified in MailerSend, the template ID does not exist, or a field fails MailerSend's validation. MailerSend's response names the field, but Nexus does not display it; send the same payload from MailerSend's API reference or check the domain and template in your MailerSend account.
- **Authentication failed for the connected service**: the API token is wrong or was deleted in MailerSend. Create a new token and update the credential under **Integrations**.
- **Permission denied by the connected service**: the token works but does not have access to the part of MailerSend the operation uses (for example a sending-only token used for **List suppressions** or **Create webhook**). Edit the token's permissions in MailerSend, or create one with the access the workflow needs.
- **The connected service did not respond in time**: for a send, the email may or may not have gone out. Sends are never retried automatically, to avoid duplicate email. Check **List messages** or **List activity** before running the workflow again.
- **The connected service is rate limiting requests**: MailerSend limits requests per account. Read operations wait and retry automatically, so on a read you only see this message when the retries run out. Sends run once and fail on the first refusal; space sends out, or combine recipients into one **Send bulk email** step.
