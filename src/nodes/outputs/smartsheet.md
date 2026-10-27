---
nav:
  title: "Smartsheet"
  position: 9
---

# Smartsheet

Use the Smartsheet node to keep Smartsheet rows in step with Shopware. Typical uses are an order tracker the fulfilment team works from, a product approval board, or a customer list, with a link to the invoice or order document attached to each row.

Pick an **Operation**: **Create or update row** finds a row by a match column and updates it, or adds a new row when none matches; **Add row** appends a row; **Update row** changes the cells of a row by its ID; **Attach link to row** stores a URL on a row; **Find rows** reads rows back, optionally filtered to one value in one column. **Cells** is a flat object of column titles and values; values are converted to each column's Smartsheet type.

:::info
**Node type:** `output.smartsheet` · **Category:** Output
:::

## Credentials

:::info
This node uses a **smartsheet** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Smartsheet, open **Account**, then **Personal Settings**, then **API Access**, and generate a new access token. It is shown only once. Generating a token needs a Business or Enterprise plan; a trial cannot create API tokens.
2. In Nexus, open **Integrations** and select **Smartsheet**.
3. Paste the token into **API access token** and pick the **Region** matching the address you sign in to: **United States (app.smartsheet.com)**, **Europe (app.smartsheet.eu)** or **Australia (app.smartsheet.au)**. A token only works in the region it was created in.
4. Choose **Test connection**.

The token acts as your own Smartsheet user, so the node reaches exactly the sheets you can already edit; nothing needs sharing.

## Operations

- **Create or update row** `upsertRow`
- **Add row** `addRow`
- **Update row** `updateRow`
- **Attach link to row** `attachLink`
- **Find rows** `findRows`

## Node parameters

Configure this node with these parameters.

### Operation

Smartsheet operation to run.

- Type: Dropdown
- Required
- Default: `upsertRow`

Options:

- **Create or update row** `upsertRow`
- **Add row** `addRow`
- **Update row** `updateRow`
- **Attach link to row** `attachLink`
- **Find rows** `findRows`

### Sheet

Sheet this step writes to or reads from. Use @ to insert prior-node data.

Pick the sheet from the list. To enter it by hand, open the sheet in Smartsheet and use File then Properties: the Sheet ID is the long number there, not the address-bar link.

- Type: Text
- Required
- Placeholder: `8244699442172292`
- Supports expressions (<code v-pre>{{ }}</code>)

### Match column

Column title whose value identifies the row. For Create or update row this is required: the row is created when no match exists and updated when exactly one row matches. For Find rows it is an optional filter, set together with Filter value. Use @ to insert prior-node data.

Pick a column whose value is unique per row, such as the order number, so repeated runs update the same row instead of adding duplicates. Make it the sheet's primary column. For Create or update row it must also be one of the columns in Cells below. For Find rows, leave both this and Filter value empty to read every row.

- Type: Text
- Optional
- Placeholder: `Order Number`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create or update row**, **Find rows**

### Cells

Flat object of column titles and values, for example {"Order Number": "<code v-pre>{{orderNumber}}</code>", "Total": "<code v-pre>{{total}}</code>"}. Values are converted to each column's actual Smartsheet type. Use @ to insert prior-node data.

Column titles must match the sheet exactly, including spelling and case; a title that does not exist fails the step and lists the sheet's real titles. A dropdown column only accepts values that are already among its options, because Smartsheet does not add options through the API. A cell holds at most 4,000 characters. Leaving a value out leaves that cell untouched: this step cannot clear a cell.

- Type: JSON
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create or update row**, **Add row**, **Update row**

### Row ID

Row this step updates or attaches to. Use @ to insert prior-node data.

Usually <code v-pre>{{rowId}}</code> from an earlier Smartsheet step. To enter it by hand, right-click the row in Smartsheet and copy its row ID.

- Type: Text
- Required
- Placeholder: <code v-pre>{{rowId}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Update row**, **Attach link to row**

### Link URL

Address of the document to attach to the row. Use @ to insert prior-node data.

The link is stored on the row; no file is uploaded. Attaching the same link again does nothing, so re-running is safe.

- Type: Text
- Required
- Placeholder: `https://example.com/invoice.pdf`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Attach link to row**

### Link name

Label shown for the attachment. Defaults to the address when left empty. Use @ to insert prior-node data.

- Type: Text
- Optional
- Placeholder: `Invoice`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Attach link to row**

### Filter value

Return only rows whose Match column holds exactly this value. Leave both empty to return every row. Use @ to insert prior-node data.

Set this together with Match column: one without the other fails the step rather than quietly returning the whole sheet.

- Type: Text
- Optional
- Placeholder: `Open`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Find rows**

### Maximum rows

Largest number of rows this step returns

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **Find rows**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Record used to fill in the row's cells |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Result | `object` | Smartsheet operation result |

**Create or update row** returns `rowId`, `rowNumber`, `action` (`created` or `updated`), `matchColumn` and `sheetId`. **Add row** returns `rowId`, `rowNumber` and `action`; **Update row** returns `rowId` and `action`. **Attach link to row** returns `attachmentId`, `action` (`created` or `existed`), `url` and `name`. **Find rows** returns `items` (each with `rowId`, `rowNumber` and `cells` keyed by column title), `count` and `hasMore`.

## Examples

### Keep an order tracker up to date and attach the invoice link

Connect a **Shopware Event Trigger** listening for **Order created or updated** (ID `order.written`), map the order with a **Field Mapping** node, and configure this node:

| Parameter | Value |
| --- | --- |
| Operation | Create or update row |
| Sheet | `8244699442172292` |
| Match column | `Order Number` |
| Cells | <code v-pre>{"Order Number": "{{data.orderNumber}}", "Customer": "{{data.orderCustomer.email}}", "Total": "{{data.amountTotal}}", "Status": "Open"}</code> |

Make **Order Number** the sheet's primary column, and make sure `Open` is an existing option if **Status** is a dropdown column. Then add a second Smartsheet node directly after it:

| Parameter | Value |
| --- | --- |
| Operation | Attach link to row |
| Sheet | `8244699442172292` |
| Row ID | <code v-pre>{{rowId}}</code> |
| Link URL | <code v-pre>https://shop.example.com/invoices/{{data.orderNumber}}.pdf</code> |
| Link name | `Invoice` |

## Related resources

- [Smartsheet API reference](https://developers.smartsheet.com/api/smartsheet/openapi)
- [Smartsheet: generate an API access token](https://help.smartsheet.com/articles/2482389-generate-API-key)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: the node checks every value against the sheet before writing and reports problems with this message, without naming the column. Check that each title in **Cells** matches a column on the sheet exactly, including spelling and case (renaming a column breaks steps that write to it); that dropdown values are already among the column's options (Smartsheet never adds options through the API); that dates are `YYYY-MM-DD` or a full timestamp, contact columns get an email address, and text stays under 4,000 characters; and that **Cells** leaves out system, formula and multi-select columns, which this node cannot write. For **Create or update row**, the match column must be in **Cells** and its value must be unique per row; pick a column such as the order number and make it the primary column. The node also writes one record per run, so a batch input fails here; split records upstream so each run carries one row.
- **Authentication failed for the connected service**: the token is wrong or revoked, was created in a different region than the one selected under **Region**, or the account requires single sign-on, which rules out personal access tokens. Check the sign-in address and the **Region**. A token that has expired shows **Authentication for the connected service has expired** instead; generate a new one and update the credential.
- **Permission denied by the connected service**: the Smartsheet user behind the token may not change the sheet (for example, it is shared as Viewer), or the target row or column is locked. Raise the sharing level or unlock the row or column in Smartsheet.
- **The requested resource was not found**: the **Sheet** or **Row ID** does not exist, or the sheet is not shared with the user the token belongs to.
- **The connected service rejected the request**: Smartsheet itself refused the write, for example because the sheet's columns changed after the node checked them, or because the sheet has two columns with the same title. Re-check the column titles on the sheet.
- **The connected service is rate limiting requests**: Smartsheet allows 300 requests per minute per token and asks for a pause of at least 60 seconds. **Update row**, **Attach link to row** and **Find rows** wait and retry; **Create or update row** and **Add row** run at most once, so a throttled attempt fails the step and the next event for the same row catches up.
- **A cell cannot be cleared**: a missing or empty value leaves the cell untouched. Clear cells in the sheet.
