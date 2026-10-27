---
nav:
  title: "Odoo"
  position: 18
---

# Odoo

Use the Odoo node to create, update, read and list Odoo records from a workflow. It works with Odoo 16 to 19, Community and Enterprise, self-hosted, Odoo.sh and Odoo Online, so you can push Shopware customers into Odoo as contacts, create sale orders or invoices from Shopware orders, and read products, stock levels and deliveries back into Shopware.

Pick a **Model** (Contacts, Products, Product variants, Sale orders, Invoices, Stock levels, Deliveries, or **Custom model…** for any technical model name), then an **Operation**: Upsert, Create, Update, Get one or List. Stock levels and deliveries are read-only, so only Get one and List are offered for them. When **Record Body** is empty, the node uses its input data, usually the output of a **Field Mapping** node.

:::info
**Node type:** `action.odoo-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **odoo** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Odoo, create a dedicated integration user with access rights to the models your workflows use.
2. Logged in as that user, open **Preferences → Account Security → New API Key** and copy the key.
3. In Nexus, open **Integrations** and select **Odoo**, then enter:
   - **Base URL**, for example `https://mycompany.odoo.com` (must use `https://`)
   - **Database** name
   - **Username**: the Odoo login email address of the integration user
   - **API key**
4. Use **Test connection** to verify the credentials before saving.

On Odoo Online, external API access is only available on the Custom pricing plan. One App Free and Standard plans reject API calls; self-hosted and Odoo.sh instances have no such restriction.

## Operations

- **Upsert (create or update)** `upsert` (only when **Model** is not one of **Stock levels (stock.quant)**, **Deliveries (stock.picking)**)
- **Create** `create` (only when **Model** is not one of **Stock levels (stock.quant)**, **Deliveries (stock.picking)**)
- **Update** `update` (only when **Model** is not one of **Stock levels (stock.quant)**, **Deliveries (stock.picking)**)
- **Get one** `get`
- **List** `list`

## Node parameters

Configure this node with these parameters.

### Model

Odoo model to operate on, such as res.partner, product.template, sale.order, or account.move

Stock levels and deliveries are read-only (get and list).

- Type: Dropdown
- Required
- Default: `res.partner`

Options:

- **Contacts (res.partner)** `res.partner`
- **Products (product.template)** `product.template`
- **Product variants (product.product)** `product.product`
- **Sale orders (sale.order)** `sale.order`
- **Invoices (account.move)** `account.move`
- **Stock levels (stock.quant)** `stock.quant`
- **Deliveries (stock.picking)** `stock.picking`
- **Custom model…** `__custom__`

### Custom model

Technical Odoo model name

Enter the model name exactly as it appears in Odoo (Settings → Technical → Models).

- Type: Text
- Required
- Placeholder: `x_project.task`
- Shown when **Model** is **Custom model…**

### Operation

Odoo operation to execute against the model

- Type: Dropdown
- Required
- Default: `upsert`

Options:

- **Upsert (create or update)** `upsert` (only when **Model** is not one of **Stock levels (stock.quant)**, **Deliveries (stock.picking)**)
- **Create** `create` (only when **Model** is not one of **Stock levels (stock.quant)**, **Deliveries (stock.picking)**)
- **Update** `update` (only when **Model** is not one of **Stock levels (stock.quant)**, **Deliveries (stock.picking)**)
- **Get one** `get`
- **List** `list`

### Record ID

Odoo record id (integer) for get or update operations

Odoo database id (integer). If you only know an external reference, use upsert with an external ID field instead.

- Type: Number
- Optional
- Placeholder: `42`
- Shown when **Operation** is one of **Get one**, **Update**

### External ID field

Unique model field used to look up an existing record for upsert (e.g. ref, default_code, client_order_ref). The value is taken from the body.

Choose a field that uniquely identifies the record, such as ref for contacts or default_code for products.

- Type: Dropdown
- Optional
- Shown when **Operation** is **Upsert (create or update)** and **Model** is not **Custom model…**

Options:

- **Customer number (ref)** `ref` (only when **Model** is **Contacts (res.partner)**)
- **Internal reference (default_code)** `default_code` (only when **Model** is one of **Products (product.template)**, **Product variants (product.product)**)
- **Customer reference (client_order_ref)** `client_order_ref` (only when **Model** is **Sale orders (sale.order)**)
- **Payment reference (payment_reference)** `payment_reference` (only when **Model** is **Invoices (account.move)**)

### External ID field

Unique model field used to look up an existing record for upsert (e.g. ref, default_code, client_order_ref). The value is taken from the body.

Unique field on your custom model used to find an existing record.

- Type: Text
- Optional
- Placeholder: `ref`
- Shown when **Operation** is **Upsert (create or update)** and **Model** is **Custom model…**

### Record Body

JSON record fields for create, update, or upsert. When empty, the node input data is used as the body. One2many/many2many fields use Odoo command tuples.

- Type: JSON
- Optional
- Placeholder: <code v-pre>{ "name": "{{customerName}}", "email": "{{email}}" }</code>
- Shown when **Operation** is one of **Create**, **Update**, **Upsert (create or update)**

### Domain

Odoo domain filter triplets for list, e.g. [["is_company","=",true]]

Use a filter to limit which records are listed, for example only contacts with an email address. In ilike filters, _ matches any single character and % matches any sequence of characters — escape them to match literally.

- Type: JSON
- Optional
- Placeholder: `[["email","!=",false]]`
- Shown when **Operation** is **List**

### Fields

Field projection, e.g. ["name","email"]. Defaults to a compact field set.

- Type: JSON
- Optional
- Shown when **Operation** is one of **List**, **Get one**

### Order

Sort expression for list. A stable id tiebreaker is applied for reliable pagination.

- Type: Text
- Optional
- Placeholder: `write_date asc`
- Shown when **Operation** is **List**

### Limit

Maximum number of records to return for list

- Type: Number
- Optional
- Default: `20`
- Shown when **Operation** is **List**

### Offset

Starting offset for list pagination

- Type: Number
- Optional
- Default: `0`
- Shown when **Operation** is **List**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input record or parameters for the Odoo operation |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Odoo API response and normalized metadata |

The output object contains the Odoo response with two Odoo quirks normalised: empty fields that Odoo reports as `false` are converted to empty values for the built-in models (custom models and unknown fields keep the raw `false`), and many2one fields such as `partner_id` arrive as `{"id": 42, "label": "Display Name"}` instead of an `[id, name]` pair. Date-times are `YYYY-MM-DD HH:MM:SS` strings in UTC without a timezone suffix. **List** returns an array of records and **Get one** a single record; deliveries additionally carry `sale_client_order_ref` from the linked sale order.

## Examples

### Create or update a contact from a Shopware customer

Connect a **Shopware Event Trigger** listening for customer changes and configure:

| Parameter | Value |
| --- | --- |
| Model | Contacts (res.partner) |
| Operation | Upsert (create or update) |
| External ID field | Customer number (ref) |
| Record Body | <code v-pre>{"ref": "{{data.customerNumber}}", "name": "{{data.firstName}} {{data.lastName}}", "email": "{{data.email}}"}</code> |

The lookup value is taken from the `ref` field in the body, so repeated runs update the same contact.

### List products that have an internal reference

| Parameter | Value |
| --- | --- |
| Model | Products (product.template) |
| Operation | List |
| Domain | `[["default_code", "!=", false]]` |
| Fields | `["name", "default_code", "list_price"]` |
| Order | `write_date asc` |
| Limit | `100` |

Domains use Odoo triplets: `[[field, operator, value], …]`.

## Related resources

- [Odoo external API](https://www.odoo.com/documentation/latest/developer/reference/external_api.html)
- [Odoo domain syntax](https://www.odoo.com/documentation/latest/developer/reference/backend/orm.html#reference-orm-domains)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Authentication failed for the connected service**: Odoo refused the login. Check the **Database** name, that **Username** is the integration user's login email address, and that the **API key** belongs to that user. On Odoo Online the same error appears when the database is not on the Custom plan, because other plans block external API access.
- **Permission denied by the connected service**: the login worked, but Odoo reports an access error for the model or record. Give the integration user access rights, or adjust the record rules, for that model in Odoo.
- **The node received data it could not process**: Odoo rejected the record itself. Typical causes are a missing required field, a value Odoo's validation refuses, or an **Update** for a **Record ID** that no longer exists. Odoo calls these validation, user or missing-record errors; check the record in Odoo and the values in **Record Body**.
- **The node configuration is invalid**: the node settings cannot be used as they are. Common causes: **Upsert** found two or more records with the same external reference (Odoo does not enforce uniqueness on `ref`, `default_code`, `client_order_ref` or `payment_reference`, and archived records count towards the match), **Domain** or **Fields** is not valid JSON, a write operation was chosen for stock levels or deliveries, **Record Body** and the input are both empty, or an expression in the configuration could not be resolved. For unresolved expressions, the details list the tokens. An upsert that matches an archived record updates it but does not reactivate it.
- **The connected service is rate limiting requests**: Odoo Online throttles bursts. **Upsert**, **Get one** and **List** wait for the interval Odoo asks for and try again; **Create** and **Update** are not retried and fail straight away. Spread out scheduled workflows if this happens often.
- **A record was created twice**: **Create** is never retried automatically, but re-running a workflow after a failed or interrupted create can duplicate the record if Odoo had already saved it. Use **Upsert** in workflows that may run more than once for the same data.
- **Sale orders and invoices appear as drafts**: the node creates them in draft state and never confirms or posts them, so no stock reservations, sequence numbers or accounting entries are triggered. Confirm them in Odoo.
