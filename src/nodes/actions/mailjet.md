---
nav:
  title: "Mailjet"
  position: 14
---

# Mailjet

Use the Mailjet node to send transactional email through Mailjet and to keep Mailjet contacts in sync with Shopware. Typical uses are an order status email, syncing newsletter recipients or customers into a Mailjet contact list, and enriching a Mailjet contact with Shopware data.

Pick an **Operation**. **Send email** sends one email with an inline **HTML Content** or **Text Content** body. **Upsert contact** creates or updates a contact by **Contact Email** and writes **Mailjet Contact Properties**; it does not touch list membership. **Manage contact list** adds, unsubscribes or removes an existing contact on one **Contact List ID**. **Create contact list** creates an empty list.

:::info
**Node type:** `action.mailjet` · **Category:** Action
:::

## Credentials

:::info
This node uses a **mailjet** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Mailjet, create an API key pair under Account settings, API Key Management, and validate the sender address or domain you will send from.
2. In Nexus, open **Integrations** and select **Mailjet**.
3. Enter the **API key** and the **API secret**.
4. Use **Test connection**, then save.

## Operations

- **Send email** `sendEmail`
- **Upsert contact** `upsertContact`
- **Manage contact list** `manageContactList`
- **Create contact list** `createContactList`

## Node parameters

Configure this node with these parameters.

### Operation

Mailjet operation to execute

- Type: Dropdown
- Required
- Default: `sendEmail`

Options:

- **Send email** `sendEmail`
- **Upsert contact** `upsertContact`
- **Manage contact list** `manageContactList`
- **Create contact list** `createContactList`

### From Email

Sender email address. Mailjet requires a validated sender or domain for live delivery.

- Type: Text
- Required
- Shown when **Operation** is **Send email**

### From Name

Optional sender display name.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### To Email

Recipient address or comma-separated addresses. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Send email**

### To Name

Optional recipient display name for single-recipient sends.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### Subject

Email subject line. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Send email**

### HTML Content

HTML email body. Provide HTML Content or Text Content. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Optional
- Shown when **Operation** is **Send email**

### Text Content

Plain-text email body. Provide Text Content or HTML Content. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Optional
- Shown when **Operation** is **Send email**

### CC

Optional CC address or comma-separated addresses.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### BCC

Optional BCC address or comma-separated addresses.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### Reply-To

Optional Reply-To address.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### Custom ID

Optional Mailjet CustomID for trace/reconciliation only. It does not deduplicate repeated sends.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### Sandbox Mode

Validate the Mailjet send request without delivering email. Leave disabled for production sends.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Send email**

### Contact Email

Contact email address used as the global Mailjet contact identity. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is one of **Upsert contact**, **Manage contact list**

### Mailjet Contact Properties

Optional Mailjet contact data properties as JSON. The placeholder is only an example; use any Mailjet property keys that already exist in the connected account and match each property's configured type.

- Type: JSON
- Optional
- Placeholder: <code v-pre>{ "firstname": "{{firstName}}", "name": "{{lastName}}", "country": "{{defaultBillingAddress.country.iso}}" }</code>
- Shown when **Operation** is **Upsert contact**

### Contact List ID

Mailjet contact list ID to update.

- Type: Text
- Required
- Shown when **Operation** is **Manage contact list**

### List Action

Consent-safe list membership action for the selected list. Force-resubscribe is intentionally not available.

- Type: Dropdown
- Required
- Default: `addnoforce`
- Shown when **Operation** is **Manage contact list**

Options:

- **Add if not unsubscribed** `addnoforce`
- **Unsubscribe** `unsub`
- **Remove from list** `remove`

### Contact List Name

Name of the empty Mailjet contact list to create. Use Manage contact list to add contacts to it.

- Type: Text
- Required
- Shown when **Operation** is **Create contact list**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Mailjet templating or contact fields |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Mailjet operation result |

The output object contains Mailjet's response. **Send email** returns the `Messages` array with the per-recipient status and message IDs; the node fails if any message has status `error`. **Upsert contact** and **Manage contact list** return the affected contact and list data; **Create contact list** returns the new list with its ID.

## Examples

### Sync a newsletter recipient into a Mailjet list

Connect a **Shopware Event Trigger** listening for **Newsletter recipient created or updated** to an **Upsert contact** node, then to a **Manage contact list** node:

| Parameter | Value |
| --- | --- |
| Operation | Upsert contact |
| Contact Email | <code v-pre>{{data.email}}</code> |
| Mailjet Contact Properties | <code v-pre>{"firstname": "{{data.firstName}}", "name": "{{data.lastName}}"}</code> |

| Parameter | Value |
| --- | --- |
| Operation | Manage contact list |
| Contact Email | <code v-pre>{{data.email}}</code> |
| Contact List ID | `123456` |
| List Action | Add if not unsubscribed |

The property keys must already exist as contact properties in your Mailjet account.

### Order update email

| Parameter | Value |
| --- | --- |
| Operation | Send email |
| From Email | `orders@yourshop.com` |
| From Name | `Your Shop` |
| To Email | <code v-pre>{{data.orderCustomer.email}}</code> |
| Subject | <code v-pre>Update on order {{data.orderNumber}}</code> |
| Text Content | <code v-pre>Your order {{data.orderNumber}} is now {{data.stateMachineState.technicalName}}.</code> |
| Custom ID | <code v-pre>{{data.orderNumber}}</code> |

## Related resources

- [Mailjet Send API v3.1](https://dev.mailjet.com/email/guides/send-api-v31/)
- [Mailjet Contact Management](https://dev.mailjet.com/email/guides/contact-management/)
- [Mailjet: Send API reference](https://dev.mailjet.com/email/reference/send-emails/)
- [Mailjet: Contact API reference](https://dev.mailjet.com/email/reference/contacts/contact/)
- [Mailjet: API Key Management](https://app.mailjet.com/account/apikeys)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The connected service rejected the request**: Mailjet refused the request. On **Send email** the usual reason is that the **From Email** address, or its domain, is not validated in Mailjet; validate it and run the workflow again. On **Manage contact list** a wrong **Contact List ID** produces this message (Mailjet reports it only as invalid object properties), so check the ID in your Mailjet account. On **Upsert contact**, check **Mailjet Contact Properties**: every key must already exist as a contact property in Mailjet with a matching type, because the node does not create property definitions.
- **The requested resource was not found**: **Manage contact list** with **Unsubscribe** or **Remove from list** only works on a contact that already exists in Mailjet. Put an **Upsert contact** step in front. **Add if not unsubscribed** creates a missing contact itself.
- **The node configuration is invalid**: a required value is empty after expressions are resolved (**From Email**, **To Email**, **Subject**, a body in **HTML Content** or **Text Content**, **Contact Email**, **Contact List ID** or **Contact List Name**), **To Name** is set while **To Email** holds more than one address, **Reply-To** holds more than one address, or **Mailjet Contact Properties** is not a JSON object. It also appears when Mailjet accepts a send but marks one of its messages as failed.
- **Authentication failed for the connected service**: the API key or secret is wrong, or the key pair was regenerated in Mailjet. Update both values in the credential under **Integrations**.
- **Nothing was delivered**: **Sandbox Mode** is enabled. Mailjet validates the request without sending. Disable it for production.
- **A failed step was not retried**: Mailjet operations run at most once, so that a timeout or a rate limit refusal never produces a duplicate email or list write. You see the message for the failure (for example **The connected service did not respond in time** or **The connected service is rate limiting requests**). Check in Mailjet whether the email went out or the contact changed, then run the workflow again if needed.
