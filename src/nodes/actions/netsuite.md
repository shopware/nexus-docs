---
nav:
  title: "Oracle NetSuite API"
  position: 20
---

# Oracle NetSuite API

Use the Oracle NetSuite API node to read and write NetSuite records through SuiteTalk REST and to run SuiteQL queries. It covers standard record types such as `customer`, `salesOrder`, `inventoryItem`, `invoice`, `itemFulfillment` and `returnAuthorization`, so you can create sales orders from Shopware orders, poll item stock into Shopware, read fulfilment status for delivery updates, or create return authorisations for refunds.

Pick an **Operation** first. Record operations (List records, Get record, Get metadata, Create, Update, Replace, Upsert by external ID, Delete, Transform) take a **Record type** and, where a single record is addressed, its **Internal ID**; Transform also needs a **Target record type**, for example turning a `salesOrder` into an `invoice` or `itemFulfillment`. Run SuiteQL takes a **SuiteQL query** instead of a record type. List and SuiteQL results are paged with **Page size**, **Maximum pages**, **Limit** and **Offset**.

:::info
**Node type:** `action.netsuite-api` · **Category:** Action
:::

:::warning
**Experimental**
This node is experimental and its behaviour may change.
:::

## Credentials

:::info
This node uses a **netsuite** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

Nexus connects with an OAuth 2.0 client credentials (machine-to-machine) integration. A NetSuite administrator prepares the following; no password, client secret or token secret is needed.

1. In NetSuite, go to **Setup → Company → Enable Features → SuiteCloud** and enable **OAuth 2.0** and **REST Web Services**.
2. Go to **Setup → Integration → Manage Integrations → New**. Name the integration, set State to Enabled, and under Authentication tick only **Client Credentials (Machine to Machine) Grant** and **REST Web Services**. Leave Authorization Code Grant and Redirect URI empty. Save and copy the **Client ID**.
3. Have your IT team create an RSA private key and a matching public certificate (for example with `openssl genrsa` and `openssl req -new -x509`). The certificate is uploaded to NetSuite; the private key is pasted into Nexus and must be kept secret.
4. Under **Setup → Users/Roles → Manage Roles**, create a role for Nexus with the platform permissions **REST Web Services** (Full), **Log in using OAuth 2.0 Access Tokens** (Full) and **SuiteAnalytics Workbook** (Edit), plus View, Create, Edit or Full on each record type your workflows use. Do not use an administrator role.
5. Under **Lists → Employees → Employees**, give the employee Nexus should act as access and add the role.
6. Go to **Setup → Integration → Manage Authentication → OAuth 2.0 Client Credentials (M2M) Setup**, create a mapping of the integration, employee and role, upload the public certificate, and copy the generated **Certificate ID**.
7. Read the **Account ID** from **Setup → Company → Company Information** (for example `1234567` or `123456_SB1` for a sandbox).
8. In Nexus, open **Integrations**, select **Oracle NetSuite** and enter **Account ID**, **OAuth client ID**, **Certificate ID** and **Private key PEM** (including the `BEGIN` and `END` lines). Use **Test connection**, then save. Nexus derives the REST and token URLs from the account ID.

## Operations

- **List records** `list`
- **Get record** `get`
- **Run SuiteQL** `suiteql`
- **Get metadata** `metadata`
- **Create record** `create`
- **Update record** `update`
- **Replace record** `replace`
- **Upsert by external ID** `upsert`
- **Delete record** `delete`
- **Transform record** `transform`

## Node parameters

Configure this node with these parameters.

### Operation

NetSuite operation to execute

- Type: Dropdown
- Required
- Default: `suiteql`

Options:

- **List records** `list`
- **Get record** `get`
- **Run SuiteQL** `suiteql`
- **Get metadata** `metadata`
- **Create record** `create`
- **Update record** `update`
- **Replace record** `replace`
- **Upsert by external ID** `upsert`
- **Delete record** `delete`
- **Transform record** `transform`

### Record type

NetSuite record type, such as customer, salesOrder, inventoryItem, or returnAuthorization

- Type: Text
- Optional
- Placeholder: `customer`
- Shown when **Operation** is one of **List records**, **Get record**, **Get metadata**, **Create record**, **Update record**, **Replace record**, **Upsert by external ID**, **Delete record**, **Transform record**

### Internal ID

NetSuite internal ID for get, update, replace, delete, or transform operations

- Type: Text
- Optional
- Shown when **Operation** is one of **Get record**, **Update record**, **Replace record**, **Delete record**, **Transform record**

### Target record type

Target record type for transform operations, such as invoice or itemFulfillment

- Type: Text
- Optional
- Shown when **Operation** is **Transform record**

### SuiteQL query

SuiteQL query for search or polling operations

- Type: Code / template
- Optional
- Shown when **Operation** is **Run SuiteQL**

### Record body

JSON body for create, update, replace, upsert, or transform operations

- Type: JSON
- Optional
- Shown when **Operation** is one of **Create record**, **Update record**, **Replace record**, **Transform record**

### Page size

Number of NetSuite records to fetch per page; NetSuite caps pages at 1000 rows

- Type: Number
- Optional
- Default: `1000`
- Shown when **Operation** is one of **List records**, **Run SuiteQL**

### Limit

Maximum number of NetSuite records to return for list or SuiteQL operations

- Type: Number
- Optional
- Shown when **Operation** is one of **List records**, **Run SuiteQL**

### Offset

Starting offset for SuiteTalk REST/SuiteQL pagination

- Type: Number
- Optional
- Default: `0`
- Shown when **Operation** is one of **List records**, **Run SuiteQL**

### Maximum pages

Maximum number of NetSuite pages to fetch

- Type: Number
- Optional
- Default: `1`
- Shown when **Operation** is one of **List records**, **Run SuiteQL**

### Expand subresources

Expand NetSuite subresource links when reading a single record

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Get record**

### Return record

Decode a response body when NetSuite returns one; most writes return Location metadata only

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Create record**, **Update record**, **Replace record**, **Upsert by external ID**, **Transform record**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input record or parameters for the NetSuite operation |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | NetSuite API response and normalized metadata |

The output object contains the SuiteTalk REST response. **List records** and **Run SuiteQL** return the fetched rows under `items`, collected across the pages you allowed. **Get record** returns the record, with linked sub-resources included when **Expand subresources** is enabled. Create, Update, Replace, Upsert and Transform normally return only the location of the written record (its internal ID); enable **Return record** to decode a record body when NetSuite sends one.

## Examples

### Poll active items with SuiteQL

| Parameter | Value |
| --- | --- |
| Operation | Run SuiteQL |
| SuiteQL query | `SELECT id, itemid, displayname FROM item WHERE isinactive = 'F'` |
| Page size | `1000` |
| Maximum pages | `5` |

The role needs **Lists → Items → View** and **SuiteAnalytics Workbook** for this query.

### Create an invoice from an existing sales order

Look up the sales order with a **Run SuiteQL** node placed directly before this one, then configure:

| Parameter | Value |
| --- | --- |
| Operation | Transform record |
| Record type | `salesOrder` |
| Internal ID | <code v-pre>{{items.0.id}}</code> |
| Target record type | `invoice` |
| Return record | enabled |

The role needs **Transactions → Sales Order → View** and **Transactions → Invoice → Create**.

## Related resources

- [NetSuite OAuth 2.0 client credentials setup](https://docs.oracle.com/en/cloud/saas/netsuite/ns-online-help/section_162686838198.html)
- [Set up OAuth 2.0 roles](https://docs.oracle.com/en/cloud/saas/netsuite/ns-online-help/section_157771510070.html)
- [Finding your NetSuite account ID](https://docs.oracle.com/en/cloud/saas/netsuite/ns-online-help/section_1498754928.html)
- [Prerequisites and setup for REST web services](https://docs.oracle.com/en/cloud/saas/netsuite/ns-online-help/article_5085602973.html)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Connection test says the private key is invalid**: the public certificate was pasted instead of the private key, or the `BEGIN` and `END` lines are missing.
- **The connected service rejected the request**: NetSuite refused the request. A frequent cause on reads is a missing record permission: NetSuite reports it as a missing record, for example `Record 'item' was not found` with the reason `INSUFFICIENT_PERMISSIONS`, even when items exist. Add **Lists → Items → View** to the role used in the OAuth 2.0 client credentials setup and run the workflow again. For `Record 'transaction' was not found`, add **Transactions → Find Transaction → View** plus View on the specific transaction types. On writes, check the **Record body** against the record's fields with **Get metadata**.
- **NetSuite permission is missing for the requested record**: NetSuite answered that the role may not perform this operation. Check the role's permission level on the record type (View, Create, Edit or Full) and, for **Run SuiteQL**, the **SuiteAnalytics Workbook** permission plus View access on every record the query reads.
- **Every NetSuite step fails after a sandbox refresh**: NetSuite clears OAuth 2.0 client credentials mappings during a refresh. Repeat the M2M setup step and update the **Certificate ID** in Nexus.
- **The wrong account is reached**: check the **Account ID**; sandbox IDs contain an underscore, for example `123456_SB1`.
- **The connected service is rate limiting requests**: NetSuite limits concurrent requests per account. Reads, **Run SuiteQL** and **Upsert by external ID** wait as long as NetSuite asks and try again; the other writes run once, because a retry could create a second record. Space out runs or lower **Maximum pages**.
- **A write returns no record data**: most SuiteTalk writes return only the new record's location. Enable **Return record**, or follow up with a **Get record** using the returned ID.
