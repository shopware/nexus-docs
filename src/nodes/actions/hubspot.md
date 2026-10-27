---
nav:
  title: "HubSpot"
  position: 10
---

# HubSpot

Use the HubSpot node to create, update, read and search HubSpot CRM records from a workflow. It supports contacts, companies, deals and products, so you can keep HubSpot contacts in sync with Shopware customers, create a deal for every Shopware order, push your Shopware catalogue into HubSpot products, or pull contacts into Shopware.

Pick an **Operation** (Upsert, Create, Update, Get one, List or Search) and an **Object Type** (Contacts, Companies, Deals or Products). Upsert uses the **ID Property** as the key: `email` by default for contacts, and a property flagged unique in HubSpot for the other types. **Record Body** is a flat map of HubSpot property names to values; when it is empty, the node uses its input data, usually the output of a **Field Mapping** node.

:::info
**Node type:** `action.hubspot-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **hubspot** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In HubSpot, create a private app under **Settings → Integrations → Private apps** and enable the CRM scopes your workflows need, for example `crm.objects.contacts.read`, `crm.objects.contacts.write` and the matching scopes for companies, deals and products.
2. Copy the private app's access token.
3. In Nexus, open **Integrations** and select **HubSpot**, then paste the token into **Private app access token**.
4. Use **Test connection** to verify the token before saving.

Private app tokens do not expire, but if you rotate or revoke the token in HubSpot, update the credential under Integrations.

## Operations

- **Upsert (create or update)** `upsert`
- **Create** `create`
- **Update** `update`
- **Get one** `get`
- **List** `list`
- **Search** `search`

## Node parameters

Configure this node with these parameters.

### Operation

HubSpot operation to execute against the CRM object type

- Type: Dropdown
- Required
- Default: `upsert`

Options:

- **Upsert (create or update)** `upsert`
- **Create** `create`
- **Update** `update`
- **Get one** `get`
- **List** `list`
- **Search** `search`

### Object Type

HubSpot CRM object type to operate on

- Type: Dropdown
- Required
- Default: `contacts`

Options:

- **Contacts** `contacts`
- **Companies** `companies`
- **Deals** `deals`
- **Products** `products`

### Record ID

HubSpot record id (numeric string) for get or update operations

- Type: Text
- Optional
- Shown when **Operation** is one of **Get one**, **Update**

### ID Property

Unique identifier property used to find an existing record for upsert. Defaults to email for contacts. Other object types require a property flagged unique in HubSpot: products can use hs_sku; deals and companies have no natural unique key, so create a custom unique property (e.g. an external id). The value is taken from the body.

- Type: Text
- Optional
- Placeholder: `email`
- Shown when **Operation** is **Upsert (create or update)**

### Record Body

JSON map of HubSpot property values for create, update, or upsert. When empty, the node input data is used as the body. All values are HubSpot properties (flat, lowercase names).

- Type: JSON
- Optional
- Shown when **Operation** is one of **Create**, **Update**, **Upsert (create or update)**

### Filters

HubSpot search filters (ANDed), e.g. [{"propertyName":"lastmodifieddate","operator":"GTE","value":"2026-01-01T00:00:00Z"}]

- Type: JSON
- Optional
- Shown when **Operation** is **Search**

### Properties

Property projection, e.g. ["email","firstname","lastname"]. Defaults to a compact property set per object type.

- Type: JSON
- Optional
- Shown when **Operation** is one of **List**, **Get one**, **Search**

### Limit

Maximum number of records per page (HubSpot caps list pages at 100).

- Type: Number
- Optional
- Default: `20`
- Shown when **Operation** is one of **List**, **Search**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input record or parameters for the HubSpot operation |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | HubSpot API response and normalized metadata |

The output object contains HubSpot's response with each record's `properties` lifted to the top level next to `id`, `createdAt`, `updatedAt` and `archived`, so the next node can reference <code v-pre>{{email}}</code> directly. Empty property values become `null`. HubSpot returns every property value as a string, including numbers, booleans and dates. **List** and **Search** return a page of records with a compact default property set unless you set **Properties**; **Get one** returns one record; Create, Update and Upsert return the affected record.

## Examples

### Create or update a contact from a Shopware customer

Connect a **Shopware Event Trigger** listening for customer changes and configure:

| Parameter | Value |
| --- | --- |
| Operation | Upsert (create or update) |
| Object Type | Contacts |
| ID Property | `email` |
| Record Body | <code v-pre>{"email": "{{data.email}}", "firstname": "{{data.firstName}}", "lastname": "{{data.lastName}}", "company": "{{data.company}}"}</code> |

### Create a deal for a new order and link the buyer

| Parameter | Value |
| --- | --- |
| Operation | Create |
| Object Type | Deals |
| Record Body | <code v-pre>{"dealname": "Order {{data.orderNumber}}", "amount": "{{data.amountTotal}}", "pipeline": "default", "dealstage": "closedwon", "_associations": {"contactEmail": "{{data.orderCustomer.email}}"}}</code> |

`_associations.contactEmail` is a control field for deals: Nexus creates or finds the contact by email and associates it with the deal. Keys starting with an underscore are never sent as properties. Deal amounts land in the portal's default currency unless you also set `deal_currency_code`.

## Related resources

- [HubSpot CRM objects API](https://developers.hubspot.com/docs/api/crm/understanding-the-crm)
- [HubSpot private apps](https://developers.hubspot.com/docs/api/private-apps)
- [HubSpot CRM search](https://developers.hubspot.com/docs/api/crm/search)
- [HubSpot API rate limits](https://developers.hubspot.com/docs/api/usage-details)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Authentication failed for the connected service**: HubSpot no longer accepts the private app token, usually because it was rotated or the app was deleted. Paste the current token into the credential under **Integrations**.
- **Permission denied by the connected service**: the private app lacks the read or write scope for the object type (HubSpot reports this as missing scopes). Add the scope on the app's Scopes tab in HubSpot, then run the workflow again.
- **The node configuration is invalid**: for **Upsert**, the **ID Property** is not flagged as a unique identifier in HubSpot. Products can use `hs_sku`; for companies and deals create a custom unique property (for example an external ID) and map your Shopware identifier to it. The same message appears when an expression in **Record Body**, **Filters** or **Properties** cannot be resolved.
- **The node received data it could not process**: HubSpot rejected a property value on **Create**, **Update** or **Upsert**, for example an unknown property name or a value that is not one of a dropdown property's options. Check the internal property names and allowed values in HubSpot's property settings.
- **The connected service is rate limiting requests**: HubSpot allows 100 to 190 requests per 10 seconds depending on your plan, and 5 search requests per second. Reads and **Upsert** wait and try again; **Create** and **Update** are not retried. When the daily quota is used up, every request fails until HubSpot resets it.
- **A HubSpot property was cleared after a sync**: a `null` in the body is sent as an empty string, which clears the property in HubSpot. Leave the field out of the body when you want to keep the existing value.
- **Upsert created a second contact for an email**: the earlier contact was archived. HubSpot creates a new contact in that case and leaves the archived one untouched.
