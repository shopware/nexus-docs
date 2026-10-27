---
nav:
  title: "ERPNext"
  position: 7
---

# ERPNext

Use the ERPNext node to create, update, read and submit ERPNext (Frappe) documents from a workflow. Typical uses are pushing Shopware orders into ERPNext as sales orders or sales invoices, creating customers when they register in Shopware, and reading items or stock levels back into Shopware on a schedule.

Pick a **DocType** (Customer, Item, Sales order, Sales invoice, Payment entry, or **Custom DocType…** for any other DocType by name), then an **Operation**: Upsert, Create, Update, Get one, List or Submit. Submit is only offered for submittable DocTypes, so it is hidden for Customer and Item. For Create and Upsert you can also enable **Auto-submit** to submit the document straight after it is written. When **Document body** is empty, the node uses its input data, usually the output of a **Field Mapping** node.

Upsert finds an existing document by the **External ID field**, or by the document name when you leave it empty. The list shows the DocType's fields with unique fields first; choose **Enter field name manually…** to type a field name into **External ID field name** instead. Sales orders, sales invoices and other documents with item rows need at least one row in `items` when they are created.

:::info
**Node type:** `action.erpnext-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **erpnext** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In ERPNext, create a dedicated integration user and give it a least-privilege role with read, write, create and (where needed) submit permission on the DocTypes you use, for example Sales User, Accounts User or Item Manager. Avoid the Administrator role.
2. On that user, open **Settings → API Access** and generate an API key and secret. The secret is shown only once.
3. In Nexus, open **Integrations** and select **ERPNext**, then enter:
   - **Base URL** of your ERPNext site (must start with `https://`)
   - **API key**
   - **API secret**
4. Use **Test connection** to verify the credentials before saving.

## Operations

- **Upsert (create or update)** `upsert`
- **Create** `create`
- **Update** `update`
- **Get one** `get`
- **List** `list`
- **Submit** `submit` (only when **DocType** is not one of **Customer**, **Item**)

## Node parameters

Configure this node with these parameters.

### DocType

ERPNext DocType to operate on, such as Customer, Item, Sales Order, or Sales Invoice

Use the ERPNext DocType name exactly as it appears in ERPNext.

- Type: Dropdown
- Required
- Default: `Customer`

Options:

- **Customer** `Customer`
- **Item** `Item`
- **Sales order** `Sales Order`
- **Sales invoice** `Sales Invoice`
- **Payment entry** `Payment Entry`
- **Custom DocType…** `__custom__`

### Custom DocType

Enter the DocType name exactly as it appears in ERPNext (case-sensitive).

- Type: Text
- Required
- Placeholder: `Warranty Claim`
- Shown when **DocType** is **Custom DocType…**

### Operation

ERPNext operation to execute against the DocType

- Type: Dropdown
- Required
- Default: `upsert`

Options:

- **Upsert (create or update)** `upsert`
- **Create** `create`
- **Update** `update`
- **Get one** `get`
- **List** `list`
- **Submit** `submit` (only when **DocType** is not one of **Customer**, **Item**)

### Document name

ERPNext document name (primary key) for get, update, or submit operations

- Type: Text
- Optional
- Placeholder: `CUST-2026-00001`
- Shown when **Operation** is one of **Get one**, **Update**, **Submit**

### External ID field

DocType field used to find an existing document for upsert. Leave empty to use the document name.

Pick a field whose value identifies one document; unique fields are listed first. po_no is not unique in ERPNext: it works while only this workflow sets it, and an upsert that matches two documents fails.

- Type: Dropdown
- Optional
- Options are loaded from your connected account
- Shown when **Operation** is **Upsert (create or update)**

### External ID field name

Enter the field name (not its label) exactly as it appears in ERPNext.

- Type: Text
- Required
- Placeholder: `external_ref`
- Shown when **Operation** is **Upsert (create or update)** and **External ID field** is **__custom__**

### Document body

JSON document fields for create, update, or upsert. When empty, the node input data is used as the body.

- Type: JSON
- Optional
- Placeholder: <code v-pre>{ "field_name": "{{value}}" }</code>
- Shown when **Operation** is one of **Create**, **Update**, **Upsert (create or update)**

### Auto-submit

Submit the document (docstatus 0 to 1) after create/upsert. Applies to submittable DocTypes like Sales Order or Sales Invoice.

Use only for documents ERPNext can submit, such as sales orders or invoices.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Create**, **Upsert (create or update)**

### Filters

Frappe filter triplets for list, e.g. [["status","=","Open"]]

- Type: JSON
- Optional
- Shown when **Operation** is **List**

### Fields

Field projection for list, e.g. ["name","customer_name"]. Defaults to name only.

- Type: JSON
- Optional
- Shown when **Operation** is **List**

### Order by

Sort expression for list, e.g. 'modified desc'. A stable tiebreaker is applied for reliable pagination.

- Type: Text
- Optional
- Placeholder: `modified desc`
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
| Input | `any` | Input document or parameters for the ERPNext operation |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | ERPNext API response and normalized metadata |

**List** returns the documents under `records`, with the fields you selected in **Fields** (only `name` by default), plus `count`, `hasMore` and `nextOffset` for paging. **Get one** returns the document as ERPNext returns it. **Create**, **Update**, **Upsert** and **Submit** return `name`, `docstatus`, `created` (true when Upsert or Create made a new document), `submitted` and the full document under `doc`, so the next node can use <code v-pre>{{name}}</code> to refer to it.

## Examples

### Create or update a sales order from a Shopware order

Map the order to an ERPNext payload with a **Field Mapping** node (customer, company, transaction date, delivery date and item rows are mandatory in ERPNext), then configure this node:

| Parameter | Value |
| --- | --- |
| DocType | Sales order |
| Operation | Upsert (create or update) |
| External ID field | `external_ref` (listed among the unique fields) |
| Auto-submit | enabled |

`external_ref` is a custom field on the Sales Order DocType marked **Unique** in ERPNext, filled with the Shopware order number. Use a unique field for the lookup: `po_no` is not unique in ERPNext and can match several orders.

### List recently changed sales orders

| Parameter | Value |
| --- | --- |
| DocType | Sales order |
| Operation | List |
| Filters | `[["modified", ">", "2026-09-01 00:00:00"]]` |
| Fields | `["name", "customer", "grand_total", "status"]` |
| Order by | `modified desc` |
| Limit | `100` |

Filters use Frappe filter triplets: `[[field, operator, value], …]`.

## Related resources

- [Frappe REST API](https://docs.frappe.io/framework/user/en/api/rest)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node received data it could not process**: ERPNext rejected the document content, usually because a mandatory field is missing (Company, a leaf Customer Group or Territory, Item Group, UOM, delivery date) or a linked record does not exist. Nexus does not show ERPNext's own message; open the document type in ERPNext, check the mandatory fields and links, and fix the mapping or the master data. The same message appears when the input data is a list of several records instead of one, or is empty, or when a sales document from the input data has no item rows.
- **The node configuration is invalid**: the node settings cannot be used as they are. Typical causes are an expression in **Document body** that does not resolve (the step fails instead of writing an empty value), a sales document in **Document body** without item rows, Upsert input that lacks a value for the **External ID field**, or an **External ID field** that matches two documents; pick a field marked Unique in ERPNext.
- **The connected service rejected the request**: ERPNext refused the request, for example **Create** for a document whose name already exists, or **Update** of a submitted document. Use **Upsert** to update existing documents; submitted documents are immutable in ERPNext and are corrected there with cancel and amend.
- **Upsert does not change a submitted document**: when the matched document is already submitted or cancelled, Upsert returns it unchanged and the step succeeds. Correct it in ERPNext with cancel and amend.
- **A document was not created after a timeout**: **Create**, **Update** and **Submit** run once and are not retried, because ERPNext commits on receipt and a retry could duplicate the document. Check ERPNext, then run the workflow again if the document is genuinely missing. **List**, **Get one** and **Upsert** are retried automatically. If Auto-submit fails after the document was written, the document stays in draft and the step reports a failure.
- **Authentication failed for the connected service**: the API key or secret is wrong, or the integration user was disabled. If ERPNext answers that the user may not access the DocType, Nexus shows **Permission denied by the connected service**; give the integration user's role the missing permission.
- **The connected service is rate limiting requests**: Frappe Cloud or your ERPNext server throttled the requests. **List**, **Get one** and **Upsert** wait as long as ERPNext asks and try again; **Create**, **Update** and **Submit** fail immediately, so space out runs.
