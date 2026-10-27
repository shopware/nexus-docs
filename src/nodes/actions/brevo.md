---
nav:
  title: "Brevo"
  position: 5
---

# Brevo

Use the Brevo node to send transactional email through Brevo and to keep Brevo contacts and lists in sync with Shopware. Typical uses are an order status email from a Brevo template, syncing customers or newsletter recipients into a Brevo list, and creating a list for a new campaign.

Pick an **Operation**. **Send email** sends one email either with inline **HTML Content** or **Text Content**, or from a Brevo **Template ID** with **Template Params**. **Upsert contact** creates or updates a contact by **Contact Email**, writes **Brevo Contact Attributes** and can add the contact to a list in the same step. **Manage contact list** adds or removes one contact on a **Contact List**. **Create contact list** creates an empty list inside a **Folder**.

:::info
**Node type:** `action.brevo` · **Category:** Action
:::

## Credentials

:::info
This node uses a **brevo** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Brevo, create an API key under Settings, SMTP & API, API Keys, and verify the sender address or domain you will send from.
2. In Nexus, open **Integrations** and select **Brevo**.
3. Enter the **API key**. Brevo keys start with `xkeysib-`.
4. Use **Test connection**, then save. The test also reports whether the account is still under validation.

## Operations

- **Send email** `sendEmail`
- **Upsert contact** `upsertContact`
- **Manage contact list** `manageContactList`
- **Create contact list** `createContactList`

## Node parameters

Configure this node with these parameters.

### Operation

Brevo operation to execute

- Type: Dropdown
- Required
- Default: `sendEmail`

Options:

- **Send email** `sendEmail`
- **Upsert contact** `upsertContact`
- **Manage contact list** `manageContactList`
- **Create contact list** `createContactList`

### From Email

Sender email address. Brevo requires a validated sender or domain for live delivery.

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

Email subject line. Required unless Template ID is set. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### HTML Content

HTML email body. Provide HTML Content or Text Content unless Template ID is set. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Optional
- Shown when **Operation** is **Send email**

### Text Content

Plain-text email body. Provide Text Content or HTML Content unless Template ID is set. Supports <code v-pre>{{field}}</code> templating.

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

### Template ID

Brevo template ID. When set, HTML/Text content are not allowed; use Template params.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### Template Params

Template variables as JSON. Requires Template ID.

- Type: JSON
- Optional
- Shown when **Operation** is **Send email**

### Tags

Optional comma-separated tags for categorization.

- Type: Text
- Optional
- Shown when **Operation** is **Send email**

### Contact Email

Contact email address used as the Brevo contact identity. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is one of **Upsert contact**, **Manage contact list**

### Sandbox Mode

Validate the Brevo send request without delivering email. No email logs are created. Leave disabled for production sends.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Send email**

### Brevo Contact Attributes

Brevo contact attributes as JSON. Keys are UPPERCASE and must already exist in the Brevo account (FIRSTNAME, LASTNAME are standard).

- Type: JSON
- Optional
- Placeholder: <code v-pre>{"FIRSTNAME": "{{firstName}}", "LASTNAME": "{{lastName}}"}</code>
- Shown when **Operation** is **Upsert contact**

### Add to Contact List

Optional Brevo list the contact is added to during the upsert.

- Type: Dropdown
- Optional
- Options are loaded from your connected account
- Shown when **Operation** is **Upsert contact**

### Custom Contact List ID

Numeric ID of the Brevo contact list.

- Type: Text
- Optional
- Placeholder: `e.g. 42`
- Shown when **Operation** is **Upsert contact** and **Add to Contact List** is **__custom__**

### Contact List

Brevo contact list to update.

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Operation** is **Manage contact list**

### Custom Contact List ID

Numeric ID of the Brevo contact list.

- Type: Text
- Required
- Placeholder: `e.g. 42`
- Shown when **Operation** is **Manage contact list** and **Contact List** is **__custom__**

### List Action

Add the contact to or remove the contact from the list.

- Type: Dropdown
- Required
- Default: `add`
- Shown when **Operation** is **Manage contact list**

Options:

- **Add to list** `add`
- **Remove from list** `remove`

### Contact List Name

Name of the empty Brevo contact list to create.

- Type: Text
- Required
- Shown when **Operation** is **Create contact list**

### Folder

Brevo folder that will contain the list.

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Operation** is **Create contact list**

### Custom Folder ID

Numeric ID of the Brevo folder.

- Type: Text
- Required
- Placeholder: `e.g. 1`
- Shown when **Operation** is **Create contact list** and **Folder** is **__custom__**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Brevo templating or contact attributes |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Brevo operation result |

**Send email** returns Brevo's response including `brevoMessageId`. **Upsert contact** returns `brevoContactEmail` and, for a newly created contact, `brevoContactId`. **Manage contact list** and **Create contact list** return Brevo's response for the list operation.

## Examples

### Order update email from a Brevo template

Connect a **Shopware Event Trigger** listening for **Order placed** to this node and configure:

| Parameter | Value |
| --- | --- |
| Operation | Send email |
| From Email | `orders@yourshop.com` |
| From Name | `Your Shop` |
| To Email | <code v-pre>{{data.orderCustomer.email}}</code> |
| To Name | <code v-pre>{{data.orderCustomer.firstName}} {{data.orderCustomer.lastName}}</code> |
| Template ID | `12` |
| Template Params | <code v-pre>{"orderNumber": "{{data.orderNumber}}", "orderTotal": "{{data.amountTotal}}"}</code> |
| Tags | `order-confirmation` |

Leave **HTML Content** and **Text Content** empty when a **Template ID** is set.

### Sync a customer into a Brevo list

| Parameter | Value |
| --- | --- |
| Operation | Upsert contact |
| Contact Email | <code v-pre>{{data.email}}</code> |
| Brevo Contact Attributes | <code v-pre>{"FIRSTNAME": "{{data.firstName}}", "LASTNAME": "{{data.lastName}}"}</code> |
| Add to Contact List | pick the list from the dropdown |

## Related resources

- [Brevo transactional email API](https://developers.brevo.com/reference/send-transac-email)
- [Brevo contact list synchronisation](https://developers.brevo.com/docs/synchronise-contact-lists)
- [Brevo: Create or update a contact](https://developers.brevo.com/reference/createcontact)
- [Brevo: Remove a contact from a list](https://developers.brevo.com/reference/removecontactfromlist)
- [Brevo: API keys](https://app.brevo.com/settings/keys/api)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: Nexus stopped the step because Brevo would otherwise accept the request and quietly lose data. On **Upsert contact**, every key in **Brevo Contact Attributes** must already exist as a contact attribute in Brevo (names are uppercase, for example `FIRSTNAME`) and its value must fit the attribute's type; create missing attributes in Brevo first. On **Send email**, **Template ID** cannot be combined with **HTML Content** or **Text Content**, and **Template Params** need a **Template ID**. The message also appears when Brevo accepted a list add but the contact is not on the list afterwards, which means the list ID is wrong.
- **The requested resource was not found**: the **Template ID** does not exist in this Brevo account (the node checks it before sending, because Brevo would otherwise accept the send and fail it later), the contact or list does not exist, or **Remove from list** targeted a contact that is not on the list. Brevo reports a removal of a non-member as a failure, and the node does not treat it as success.
- **Authentication failed for the connected service**: the API key is wrong or was deleted in Brevo. Create a new key and update the credential under **Integrations**.
- **Permission denied by the connected service**: most often the Brevo account has not been activated for transactional sending yet (Brevo reports the account as under validation). There is nothing to change in Nexus; wait for Brevo to activate the account. **Test connection** on the credential shows this state.
- **The node failed unexpectedly**: on a Brevo node this usually means the account has run out of email credits (Brevo reports not enough credits). It is not retried. Top up the plan in Brevo and run the workflow again.
- **Emails arrive from a brevosend.com address**: the **From Email** is not a verified sender. Brevo rewrites unverified senders instead of rejecting the send. Verify the address or domain in Brevo.
- **No email logs for a send**: **Sandbox Mode** was enabled. Brevo validates the request and returns a message ID but delivers nothing and logs nothing.
