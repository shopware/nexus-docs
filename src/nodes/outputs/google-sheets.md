---
nav:
  title: "Google Sheets"
  position: 5
---

# Google Sheets

Use the Google Sheets node to keep rows in a Google spreadsheet in step with Shopware. Typical uses are an order tracker the operations team works from, a CRM-style customer list, or a product list a partner reads. The node writes and reads rows in one tab of the spreadsheet bound to the credential.

Pick an **Operation**: **Create or update row** finds a row by a match column and updates it, or adds a new row when none matches; **Add row** appends a row unconditionally; **Update row** changes the cells of one existing row found by a match column; **Find rows** reads rows back; **Create tab** gets or creates a tab and writes its column headings. The header row of the tab is the schema: **Cells** is a flat object of column headings and values.

:::info
**Node type:** `output.google-sheets` · **Category:** Output
:::

## Credentials

:::info
This node uses a **google-sheets** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Google Cloud, create a service account, enable the **Google Sheets API** for its project, and download the service account's JSON key.
2. Open the target spreadsheet in Google Sheets and share it with the service account's email address with **Editor** access.
3. In Nexus, open **Integrations** and select **Google Sheets**.
4. Paste the whole key file into **Service account key (JSON)**, enter the **Spreadsheet ID** (the part of the spreadsheet's address between `/d/` and `/edit`) and optionally a **Spreadsheet name (optional)**.
5. Choose **Test connection**. The test proves the key works and the spreadsheet can be opened; writing also needs the Editor share.

A Google Workspace account is the supported configuration. One credential addresses one spreadsheet, so create one credential per spreadsheet. Give the node a tab of its own: **Add row** inserts where the table ends, so anything kept below the rows moves down.

## Operations

- **Create or update row** `upsertRow`
- **Add row** `appendRow`
- **Update row** `updateRow`
- **Find rows** `findRows`
- **Create tab** `addSheet`

## Node parameters

Configure this node with these parameters.

### Operation

Google Sheets operation to run.

- Type: Dropdown
- Required
- Default: `upsertRow`

Options:

- **Create or update row** `upsertRow`
- **Add row** `appendRow`
- **Update row** `updateRow`
- **Find rows** `findRows`
- **Create tab** `addSheet`

### Tab

Tab inside the spreadsheet the credential points at. Tabs are addressed by their ID, so renaming a tab does not break the step.

Pick the tab from the list. If the list is empty, the credential is not connected yet or the spreadsheet is not shared with the service account; choose Enter ID manually and type the ID instead. Give this connector a tab of its own: Add row inserts at the end of the table, so anything kept below the rows moves down.

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Operation** is one of **Create or update row**, **Add row**, **Update row**, **Find rows**

### Tab ID

Tab ID, used when the tab list cannot be loaded or the ID comes from an earlier step. Use @ to insert prior-node data.

Open the tab in Google Sheets and read the number after gid= in the address bar. A Create tab step also returns the ID it created, so <code v-pre>{{sheetId}}</code> from that step works here.

- Type: Text
- Required
- Placeholder: `0`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create or update row**, **Add row**, **Update row**, **Find rows** and **Tab** is **__custom__**

### Match column

Column heading whose value identifies the row. Required for Create or update row and Update row. For Find rows it is an optional filter, set together with Match value. Use @ to insert prior-node data.

Pick a heading whose value is unique per row, such as the order number, so repeated runs update the same row instead of adding duplicates. For Create or update row the heading must also be one of the keys in Cells below, because that is where the value comes from. For Update row and Find rows the value comes from Match value instead. For Find rows, leave both empty to read every row.

- Type: Text
- Optional
- Placeholder: `Order Number`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create or update row**, **Update row**, **Find rows**

### Match value

Value the Match column must hold. For Update row this identifies the row to change. For Find rows it filters the rows returned. Use @ to insert prior-node data.

Matching is exact. A text value never matches a number that only looks the same, so 00123 and 123 are different rows. For Find rows, set this together with Match column: one without the other fails the step rather than quietly returning the whole tab.

- Type: Text
- Optional
- Placeholder: <code v-pre>{{orderNumber}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Update row**, **Find rows**

### Cells

Flat object of column headings and values, for example {"Order Number": "<code v-pre>{{orderNumber}}</code>", "Total": "<code v-pre>{{total}}</code>"}. Headings come from the tab's header row. Use @ to insert prior-node data.

Headings must match the header row exactly, including spelling and case. Leaving a value out leaves that cell untouched: this step cannot clear a cell. Text is stored exactly as given, so a value starting with = is stored as text and never becomes a formula. Dates are stored as text in ISO format, which sorts correctly. For Update row, do not include the Match column here: the key stays as it is.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create or update row**, **Add row**, **Update row**

### Header row

Row number that holds the column headings

- Type: Number
- Optional
- Default: `1`
- Shown when **Operation** is one of **Create or update row**, **Add row**, **Update row**, **Find rows**

### Maximum rows

Largest number of rows this step returns

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **Find rows**

### Tab name

Name of the tab to create. An existing tab with this name is reused rather than duplicated. Use @ to insert prior-node data.

Google treats tab names as case-insensitive, so a tab whose name differs only in letter case counts as the same name and the step fails rather than writing to the wrong tab.

- Type: Text
- Required
- Placeholder: `Orders`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create tab**

### Column headings

List of column headings written to row 1 of a newly created tab, for example ["Order Number", "Customer Email", "Total"]. Ignored when the tab already exists.

Headings may contain letters, digits, spaces, underscores and hyphens, and must start with a letter or underscore, because later steps address cells by heading. Blank entries are rejected: a gap in the first heading would leave a tab that Add row cannot write to.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create tab**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Record used to fill in the row's cells |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Result | `object` | Google Sheets operation result |

**Find rows** returns `items`, one object per row keyed by the column headings and carrying the row's number in the sheet, plus `count` and `hasMore`. **Create tab** returns the ID of the tab (`sheetId`), which a Google Sheets node directly after it can use as its **Tab ID**. The row-writing operations return a result object for the written row.

## Examples

### Keep an order tracker up to date

Connect a **Shopware Event Trigger** listening for **Order created or updated** (ID `order.written`), map the order with a **Field Mapping** node, and configure:

| Parameter | Value |
| --- | --- |
| Operation | Create or update row |
| Tab | Orders |
| Match column | `Order Number` |
| Cells | <code v-pre>{"Order Number": "{{data.orderNumber}}", "Customer Email": "{{data.orderCustomer.email}}", "Total": "{{data.amountTotal}}", "Status": "{{data.stateMachineState.name}}"}</code> |
| Header row | `1` |

The match column heading must also be one of the keys in **Cells**, because that is where the match value comes from. Repeated runs for the same order update the same row.

### Create the tab with its headings first

| Parameter | Value |
| --- | --- |
| Operation | Create tab |
| Tab name | `Orders` |
| Column headings | `["Order Number", "Customer Email", "Total", "Status"]` |

## Related resources

- [Google Sheets API reference](https://developers.google.com/workspace/sheets/api/reference/rest)
- [Google Cloud: create a service account and key](https://cloud.google.com/iam/docs/keys-create-delete)
- [Google Sheets API: usage limits](https://developers.google.com/workspace/sheets/api/limits)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: the node reports almost every setup problem with this one message and no further detail, so work through the likely causes. Credential: Google rejected the service-account key (deleted, disabled or rotated, the Google Sheets API not enabled on its project, or a clock that is out of step), or the **Spreadsheet ID** is wrong (paste only the part between `/d/` and `/edit`). Access: the spreadsheet is not shared with the service account as **Editor**, or the target rows sit inside a protected range. Tab: the selected **Tab** or the **Tab ID** does not exist in the spreadsheet, or **Create tab** used a name that already exists (Google ignores letter case). Headings: a heading in **Cells** or the **Match column** is not in the header row exactly, including letter case; the header row has two columns with the same heading, an empty first heading, or a heading that does not start with a letter or underscore or contains characters other than letters, digits, spaces, underscores and hyphens. Matching: **Cells** lacks the **Match column**, **Update row** found no matching row, or more than one row carries the match value. A <code v-pre>{{...}}</code> token that the input cannot resolve also ends here.
- **The connected service is rate limiting requests**: Google allows 60 reads and 60 writes per minute per service account, shared by every credential built on the same key. **Create or update row** makes several reads per run, so frequent runs saturate the quota quickly. **Create or update row**, **Create tab** and **Find rows** retry with backoff; **Add row** and **Update row** run at most once, because a retry could duplicate a row or overwrite a newer edit, so a throttled attempt fails straight away. Use a separate service account in a separate Cloud project for more throughput.
- **The node received data it could not process**: either the tab changed between the node reading and writing it (nothing was written; run the step again), or the write went out but its result could not be confirmed. In the second case the row may already be written, so check the tab before running the step again.
- **A new row is added on every run instead of updating**: the match value does not match exactly. Text never matches a number that only looks the same (`00123` is not `123`), and comparison is case-sensitive. Store the identifier the same way in the sheet and in the mapping.
- **A cell cannot be cleared**: leaving a value out of **Cells** leaves the cell untouched, and an empty string is treated the same way. Clear cells in the spreadsheet.
- **A value starting with = is stored as text**: values are never interpreted as formulas. Add a formula column of your own for calculations.
