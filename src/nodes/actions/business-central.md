---
nav:
  title: "Microsoft Dynamics Business Central"
  position: 16
---

# Microsoft Dynamics Business Central

Use the Microsoft Dynamics Business Central node to read and write Business Central data from a workflow. It supports the standard Business Central API v2.0 entities such as items, customers, sales orders and vendors, so you can sync catalogue data into Shopware, push Shopware orders into Business Central, or look up records while a workflow runs.

Pick an **Entity Type**, then an **Operation**. Read operations are available for every entity. Write operations (create or update, delete, run action) are available for items, customers, sales orders and vendors.

:::info
**Node type:** `action.business-central-api` · **Category:** Action
:::

:::warning
**Experimental**
This node is experimental and its behaviour may change.
:::

## Credentials

:::info
This node uses a **business-central** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **Microsoft Dynamics 365 Business Central**.
2. Choose **Connect** to sign in with Microsoft and pick the environment and company, or choose **Manual setup** and enter:
   - **Azure AD Tenant ID** (Azure Portal → Microsoft Entra ID → Overview)
   - **Client ID** and **Client secret** of an app registration that has Business Central API permissions
   - **BC Environment Name** (for example `Production` or `Sandbox`)
   - **Company ID** (Business Central → Settings → Company Information)
3. Use **Test Connection** to verify the credentials before saving.

The app registration needs read permission on sales invoices and sales shipments as well as on the entities you work with, because create-or-update checks posted documents to avoid duplicates.

## Operations

- **Get all records** `getAll`
- **Get one record** `getOne`
- **Create or update record** `createOrUpdate` (only when **Entity Type** is one of **Items (products)**, **Customers**, **Sales orders**, **Vendors (suppliers)**)
- **Delete record** `delete` (only when **Entity Type** is one of **Items (products)**, **Customers**, **Sales orders**, **Vendors (suppliers)**)
- **Run action** `action` (only when **Entity Type** is one of **Items (products)**, **Customers**, **Sales orders**, **Vendors (suppliers)**)

## Node parameters

Configure this node with these parameters.

### Entity Type

Business Central entity to operate on

- Type: Dropdown
- Required

Options:

- **Items (products)** `items`
- **Customers** `customers`
- **Sales orders** `salesOrders`
- **Vendors (suppliers)** `vendors`
- **Purchase orders** `purchaseOrders`
- **Sales invoices** `salesInvoices`
- **Purchase invoices** `purchaseInvoices`
- **Sales quotes** `salesQuotes`
- **Sales shipments** `salesShipments`
- **Item categories** `itemCategories`
- **Item variants** `itemVariants`
- **Contacts** `contacts`
- **Locations** `locations`

### Operation

Action to perform on the selected entity

- Type: Dropdown
- Required

Options:

- **Get all records** `getAll`
- **Get one record** `getOne`
- **Create or update record** `createOrUpdate` (only when **Entity Type** is one of **Items (products)**, **Customers**, **Sales orders**, **Vendors (suppliers)**)
- **Delete record** `delete` (only when **Entity Type** is one of **Items (products)**, **Customers**, **Sales orders**, **Vendors (suppliers)**)
- **Run action** `action` (only when **Entity Type** is one of **Items (products)**, **Customers**, **Sales orders**, **Vendors (suppliers)**)

### Filter

Filter records using OData query expressions

- Type: OData filter
- Optional
- Shown when **Operation** is **Get all records**

### Max Records

Maximum number of records to retrieve

- Type: Number
- Optional
- Placeholder: `All records`
- Shown when **Operation** is **Get all records**

### Sync only new and updated data

After the first run, only new or updated data will be synced. Deleted or unchanged data will not be updated automatically.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Get all records**

### Entity ID

Entity identifier for getOne or delete

- Type: Text
- Optional
- Shown when **Operation** is one of **Get one record**, **Delete record**

### ETag

Optional ETag value from the latest record read

Use ETag for optimistic concurrency to avoid overwriting stale data.

- Type: Text
- Optional
- Shown when **Operation** is **Delete record**

### Action Name

Business Central action to execute on the entity

- Type: Text
- Optional
- Placeholder: `shipAndInvoice`
- Shown when **Operation** is **Run action**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Business Central API response |

The output object contains the Business Central API response. **Get all records** returns the records under `value`; **Get one record**, **Create or update record** and **Run action** return the affected record; **Delete record** returns an empty result on success.

## Examples

### Create or update a sales order from a Shopware order

Map the Shopware order to a Business Central payload with a **Field Mapping** node, then configure this node:

| Parameter | Value |
| --- | --- |
| Entity Type | Sales orders |
| Operation | Create or update record |

The mapped payload must include a non-empty `externalDocumentNumber` (for example the Shopware order number). Nexus uses it to find an existing open order and update it; if a posted invoice or shipment already carries that number, the order is not created again.

### Read low-stock items

| Parameter | Value |
| --- | --- |
| Entity Type | Items (products) |
| Operation | Get all records |
| Filter | `inventory lt 10` |
| Max Records | `500` |

Filters use OData syntax, for example `email eq 'john@example.com'`, `status eq 'Open'` or `externalDocumentNumber eq 'SW-10001'`.

## Related resources

- [Business Central API v2.0 reference](https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/api-reference/v2.0/)
- [Using filter expressions in OData URIs](https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/webservices/use-filter-expressions-in-odata-uris)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: for **Create or update record** on sales orders, the payload has no `externalDocumentNumber`, or a posted sales invoice or shipment already carries that number, so Nexus refuses to create the order again. The same message appears when the lookup finds more than one record with the same number, or when an earlier attempt for that number ended without a clear answer from Business Central (see the next entry). The step shows no further detail, so check these causes in order.
- **A sales order is not created again after a failed run**: when a create attempt timed out or Business Central answered with a server error, Nexus cannot tell whether the order was written, so it blocks every further automatic create for that external document number to avoid a duplicate. Look for the order in Business Central. If it exists, the next run finds it and updates it. If it is genuinely missing, create it in Business Central with the same external document number; later runs then update it. A plain rejection from Business Central, such as a validation error, does not block a later attempt.
- **The node failed unexpectedly**: on **Delete record**, Business Central refused the delete because the record changed since it was read. Read it again and pass the fresh **ETag**.
- **Authentication failed for the connected service**: the client secret expired or was replaced, or the app registration no longer has access. Re-authorise the credential under **Integrations**. If Business Central answers that the app may not access the entity, Nexus shows **Permission denied by the connected service**; check the app registration's Business Central API permissions.
- **The connected service is rate limiting requests**: Business Central throttled the requests. Reads wait as long as Business Central asks and try again; writes run once, so space out runs.
- **Sync only new and updated data returns the same records again**: the first run returns everything and stores a position; later runs return only records created or modified since. The position is saved only when the whole workflow run succeeds. Deleted records are never reported, so handle deletions separately.
