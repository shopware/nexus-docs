---
nav:
  title: "SendGrid API"
  position: 28
---

# SendGrid API

Use the SendGrid API node to send transactional email through Twilio SendGrid, keep Marketing Contacts in sync with Shopware, and read delivery and suppression data back into a workflow. Typical uses are an order confirmation from a dynamic template, a shipping notification, syncing newsletter recipients into a marketing list, and a scheduled poll of bounces or spam reports that opts recipients out in Shopware.

Pick an **Operation**. **Send email** and **Add or update contacts** work in two modes: with **Use raw payload** checked you write the SendGrid JSON body yourself; unchecked, you fill in labelled fields such as **To Email**, **Subject**, **Template ID** and **Template Data**. **Search contacts** and the two suppression operations take a list of **Emails**. The **Advanced** operations read, page through, create, update and delete records of an **Entity type** (lists, templates, unsubscribe groups, bounces, email activity and more), or send a raw request to any SendGrid v3 path.

:::info
**Node type:** `action.sendgrid-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **sendgrid** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In SendGrid, verify the sender identity or domain you will send from, and create an API key with the scopes the workflow needs: Mail Send for sending, Marketing for contacts and lists, Suppressions for suppression reads and writes, and Email Activity to read the messages entity.
2. In Nexus, open **Integrations** and select **SendGrid**.
3. Enter the **API key** (it starts with `SG.`), the **Verified sender** address and a **Sender name**. The verified sender is used as the From address whenever a node does not set one.
4. Use **Test connection**, then save.

## Operations

- **Send email** `sendMail`
- **Add or update contacts** `upsertContacts`
- **Search contacts** `searchContacts`
- **Add global suppression** `addGlobalSuppression`
- **Add group suppression** `addGroupSuppression`
- **Advanced: list records** `list`
- **Advanced: list one page** `listPage`
- **Advanced: get record** `get`
- **Advanced: create record** `create`
- **Advanced: update record** `update`
- **Advanced: delete record** `delete`
- **Advanced: raw GET request** `getJSON`
- **Advanced: raw request** `genericRequest`

## Node parameters

Configure this node with these parameters.

### Operation

SendGrid REST v3 operation to execute.

- Type: Dropdown
- Required
- Default: `sendMail`

Options:

- **Send email** `sendMail`
- **Add or update contacts** `upsertContacts`
- **Search contacts** `searchContacts`
- **Add global suppression** `addGlobalSuppression`
- **Add group suppression** `addGroupSuppression`
- **Advanced: list records** `list`
- **Advanced: list one page** `listPage`
- **Advanced: get record** `get`
- **Advanced: create record** `create`
- **Advanced: update record** `update`
- **Advanced: delete record** `delete`
- **Advanced: raw GET request** `getJSON`
- **Advanced: raw request** `genericRequest`

### Use raw payload

Keep checked to write the SendGrid JSON body yourself. Uncheck to fill in labeled fields such as To, Subject, and HTML content instead.

- Type: Toggle
- Optional
- Default: `true`
- Shown when **Operation** is one of **Send email**, **Add or update contacts**

### From Email

Sender address. Leave empty to use the verified sender configured on the SendGrid credential.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### From Name

Optional sender display name.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### To Email

Recipient address or comma-separated addresses.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### To Name

Optional recipient display name for single-recipient sends.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### Subject

Email subject line. Required unless Template ID is set or the template supplies it.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### HTML Content

HTML email body. Provide HTML Content or Text Content unless Template ID is set.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### Text Content

Plain-text email body. Provide Text Content or HTML Content unless Template ID is set.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### CC

Optional CC address or comma-separated addresses.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### BCC

Optional BCC address or comma-separated addresses.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### Reply-To

Optional Reply-To address.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### Template Data

Dynamic template variables as JSON. Requires Template ID. Use @ to insert prior-node data.

- Type: JSON
- Optional
- Placeholder: <code v-pre>{"orderNumber": "{{orderNumber}}"}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False**

### First name

Contact first name for the Marketing Contacts upsert.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Add or update contacts** and **Use raw payload** is **False**

### Last name

Contact last name for the Marketing Contacts upsert.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Add or update contacts** and **Use raw payload** is **False**

### Entity type

SendGrid entity for reads, writes, and diagnostics. Messages and events are read-only delivery truth surfaces.

- Type: Dropdown
- Optional
- Default: `messages`
- Shown when **Operation** is one of **Advanced: list records**, **Advanced: list one page**, **Advanced: get record**, **Advanced: create record**, **Advanced: update record**, **Advanced: delete record**

Options:

- **Account** `account`
- **User credits** `credits`
- **Scopes** `scopes`
- **Marketing contacts** `contacts`
- **Marketing lists** `lists`
- **List contacts** `listContacts`
- **Contact custom fields** `customFields`
- **Dynamic templates** `templates`
- **Template versions** `templateVersions`
- **Unsubscribe groups** `asmGroups`
- **Global suppressions** `globalSuppressions`
- **Group suppressions** `groupSuppressions`
- **Bounces** `bounces`
- **Blocks** `blocks`
- **Invalid emails** `invalidEmails`
- **Spam reports** `spamReports`
- **Messages / email activity** `messages`
- **Stats** `stats`
- **Category stats** `categoryStats`
- **Event webhook settings** `eventWebhooks`
- **Parse webhook settings** `parseWebhooks`
- **Mock events** `mockEvents`
- **Generic API** `genericApi`

### Record ID

ID for get, update, or delete operations.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Advanced: get record**, **Advanced: update record**, **Advanced: delete record** and **Entity type** is not one of **Account**, **User credits**, **Scopes**, **Stats**, **Category stats**, **Event webhook settings**, **Global suppressions**, **Group suppressions**, **Bounces**, **Blocks**, **Invalid emails**, **Spam reports**, **Template versions** or **Operation** is one of **Advanced: list records**, **Advanced: list one page** and **Entity type** is **List contacts**

### Email

Single email address for contact, suppression, bounce, block, invalid-email, or spam-report lookups.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Advanced: get record**, **Advanced: delete record** and **Entity type** is one of **Global suppressions**, **Group suppressions**, **Bounces**, **Blocks**, **Invalid emails**, **Spam reports**

### Contact Email

Email address identifying the contact to add or update. Supports @ to insert prior-node data.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Add or update contacts** and **Use raw payload** is **False**

### Emails

Email addresses for contact search or suppression writes. Accepts a JSON array or an expression.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Search contacts**, **Add global suppression**, **Add group suppression**

### ASM group ID

Unsubscribe group ID for ASM group suppression reads or writes.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Add group suppression** or **Operation** is one of **Advanced: list records**, **Advanced: list one page**, **Advanced: get record**, **Advanced: delete record** and **Entity type** is **Group suppressions**

### Template ID

Dynamic template ID for template version operations.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Send email** and **Use raw payload** is **False** or **Operation** is one of **Advanced: get record**, **Advanced: create record**, **Advanced: update record**, **Advanced: delete record** and **Entity type** is **Template versions**

### Template version ID

Template version ID for get, update, or delete operations.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Advanced: get record**, **Advanced: update record**, **Advanced: delete record** and **Entity type** is **Template versions**

### Generic path

Relative SendGrid v3 path for generic requests, for example /user/account.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Advanced: raw GET request**, **Advanced: raw request**

### Generic method

HTTP method for generic requests.

- Type: Dropdown
- Optional
- Default: `GET`
- Shown when **Operation** is **Advanced: raw request**

Options:

- **GET** `GET`
- **POST** `POST`
- **PUT** `PUT`
- **DELETE** `DELETE`
- **PATCH** `PATCH`

### Search query

SendGrid search query string, primarily for Marketing Contacts search.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Search contacts**

### Query parameters

Optional query parameters for reads and generic requests, such as start_time, end_time, query, page_size, page_token, or limit.

- Type: Key-value list
- Optional
- Shown when **Operation** is one of **Advanced: list records**, **Advanced: list one page**, **Advanced: get record**, **Advanced: raw GET request**, **Advanced: raw request**

### Page size

Maximum records per SendGrid page when the endpoint supports page_size.

- Type: Number
- Optional
- Shown when **Operation** is one of **Advanced: list records**, **Advanced: list one page**

### Page token

SendGrid page_token for listPage operations.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Advanced: list one page**

### Limit

Endpoint-specific result limit, including Email Activity diagnostic reads.

- Type: Number
- Optional
- Shown when **Operation** is one of **Advanced: list records**, **Advanced: list one page**, **Search contacts**

### Max records

Maximum records to collect across paginated list operations. Leave empty to use the connector default.

- Type: Number
- Optional
- Shown when **Operation** is **Advanced: list records**

### Payload

JSON payload for SendGrid write operations. Use custom_args for Shopware/Nexus correlation.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Send email**, **Add or update contacts** and **Use raw payload** is **True** or **Operation** is one of **Advanced: create record**, **Advanced: update record** or **Operation** is **Advanced: raw request** and **Generic method** is not one of **GET**, **DELETE**

### List IDs

Marketing list IDs to attach during contact upsert. Accepts a JSON array or an expression.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Add or update contacts**

### Allow suppression deletes

Advanced safety gate for workflows that remove suppression or negative-deliverability evidence. Default presets should leave this off.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Advanced: delete record** and **Entity type** is one of **Global suppressions**, **Group suppressions**, **Bounces**, **Blocks**, **Invalid emails**, **Spam reports**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for SendGrid template interpolation and payload mapping |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | SendGrid API response. Mail Send returns accepted/queued metadata, not delivery proof. |

**Send email** returns accepted or queued metadata and SendGrid's message ID; SendGrid accepting the message is not proof of delivery. **Add or update contacts** returns a `job_id` for the asynchronous import. **Advanced: list records** returns the collected records; **Advanced: list one page** returns one page plus a `next` page token. Other operations return SendGrid's response as-is.

## Examples

### Send dynamic template email

Send an order confirmation and preserve Shopware IDs in custom_args.

```json
{
  "operation": "sendMail",
  "payload": {
    "template_id": "d-forge-sendgrid-order-confirmation",
    "personalizations": [],
    "custom_args": {
      "shopware_order_id": "{{order.id}}"
    }
  }
}
```

### Read delivery activity

Poll Email Activity for delivered, bounced, dropped, spam, and unsubscribe events.

```json
{
  "operation": "list",
  "entity": "messages",
  "queryParams": {
    "limit": 100
  },
  "maxRecords": 500
}
```

### Shipping notification with the labelled fields

Connect a **Shopware Event Trigger** listening for **Order delivery created or updated** to this node, uncheck **Use raw payload** and configure:

| Parameter | Value |
| --- | --- |
| Operation | Send email |
| Use raw payload | unchecked |
| To Email | <code v-pre>{{data.order.orderCustomer.email}}</code> |
| Template ID | `d-xxxxxxxxxxxxxxxxxxxxxxxx` |
| Template Data | <code v-pre>{"order_number": "{{data.order.orderNumber}}", "tracking_code": "{{data.trackingCodes.0}}"}</code> |

**From Email** can stay empty; the verified sender on the credential is used.

### Sync a newsletter recipient into a marketing list

| Parameter | Value |
| --- | --- |
| Operation | Add or update contacts |
| Use raw payload | unchecked |
| Contact Email | <code v-pre>{{data.email}}</code> |
| First name | <code v-pre>{{data.firstName}}</code> |
| Last name | <code v-pre>{{data.lastName}}</code> |
| List IDs | `["your-list-id"]` |

## Related resources

- [SendGrid Mail Send API](https://www.twilio.com/docs/sendgrid/api-reference/mail-send/mail-send)
- [SendGrid Marketing Contacts API](https://www.twilio.com/docs/sendgrid/api-reference/contacts)
- [SendGrid Event Webhook](https://www.twilio.com/docs/sendgrid/for-developers/tracking-events/event)
- [SendGrid: API keys](https://www.twilio.com/docs/sendgrid/ui/account-and-settings/api-keys)
- [SendGrid: Sender verification](https://www.twilio.com/docs/sendgrid/ui/sending-email/sender-verification)
- [SendGrid: Email Activity API](https://www.twilio.com/docs/sendgrid/api-reference/email-activity)
- [SendGrid: Global suppressions API](https://www.twilio.com/docs/sendgrid/api-reference/suppressions-global-suppressions)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Permission denied by the connected service**: SendGrid refused the request. On **Send email** the usual reason is a From address that is not a verified sender or on an authenticated domain; verify it in SendGrid, or leave **From Email** empty to use the credential's verified sender. On other operations the API key lacks the scope for that part of SendGrid (Mail Send, Marketing, Suppressions and so on); edit the key's permissions in SendGrid. Reading the **Messages / email activity** entity also needs SendGrid's paid Email Activity add-on; without it, poll **Bounces**, **Spam reports** or **Invalid emails** instead.
- **Authentication failed for the connected service**: the API key is wrong or was deleted in SendGrid. Create a new key and update the credential under **Integrations**.
- **The connected service rejected the request**: SendGrid did not accept the request body. A common case on **Add or update contacts** is a custom field that does not exist as a field definition in SendGrid; create it first, for example with **Advanced: create record** on the **Contact custom fields** entity. With **Use raw payload** checked, also check the JSON in **Payload** against SendGrid's API reference.
- **The node configuration is invalid**: Nexus stopped the step before calling SendGrid. Common causes: **Advanced: delete record** on a suppression entity (bounces, blocks, spam reports, invalid emails or suppressions) without **Allow suppression deletes** checked, which is required because removing the entry re-enables sending to that address; an empty **Contact Email** on **Add or update contacts**; **Template Data** that does not resolve to a JSON object; or an empty **Payload** where the operation needs one.
- **The requested resource was not found**: the **Record ID**, **ASM group ID** or **Generic path** does not point at an existing record in this SendGrid account.
- **The connected service is rate limiting requests**: reads wait and retry automatically, so on a read you only see this message when the retries run out. Sends and every other write, including **Add or update contacts**, fail on the first rate limit refusal and are not retried, so a send is never delivered twice. Check SendGrid before running the workflow again.
- **Advanced: list records stops at 1000 records**: that is the default cap. Set **Max records** (up to 10000) for a deliberate bulk read, or use **Advanced: list one page** with **Page token** to page manually.
