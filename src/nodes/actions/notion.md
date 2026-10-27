---
nav:
  title: "Notion"
  position: 17
---

# Notion

Use the Notion node to read and write Notion data from a workflow: query the entries of a database, create or update an entry, create a page, or append content to an existing page. It is well suited to lightweight operational boards, for example an order tracker that gets a row per Shopware order, a product approval board, or a customer list that stays in sync with Shopware.

Pick an **Operation**. **Create or update data source entry** takes a **Data source ID**, a **Match property** used to find an existing entry, and a flat **Properties** object of Notion property names and values; the entry is created when nothing matches and updated when exactly one entry matches. **Query data source entries** reads entries from a **Data source ID**, optionally narrowed with a Notion **Filter** and ordered with **Sorts**, **Page size** entries per run. **Create page** takes the parent **Page ID**, a **Title** and optional **Content**; **Append page content** takes the **Page ID** and the **Content** to add. For both, **Content format** decides whether the text is written as plain paragraphs or converted from Markdown. The write operations handle one record per execution.

:::info
**Node type:** `action.notion` · **Category:** Action
:::

## Credentials

:::info
This node uses a **notion** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In the Notion Developer portal, create an internal integration for your workspace and copy the integration token (`ntn_…`) from its **Configuration** tab. The token is bound to that one workspace.
2. In Notion, open every database and page the workflows write to and add the integration under the page's **Connections** menu. An unshared database or page is reported as not found even though the token is valid.
3. In Nexus, open **Integrations** and select **Notion**, then paste the token into **Integration token**.
4. Use **Test connection** to verify the token; the test reports the connected workspace.

## Operations

- **Create or update data source entry** `upsertDataSourcePage`
- **Query data source entries** `queryDataSource`
- **Create page** `createPage`
- **Append page content** `appendPageContent`

## Node parameters

Configure this node with these parameters.

### Operation

Notion operation to run.

- Type: Dropdown
- Required
- Default: `upsertDataSourcePage`

Options:

- **Create or update data source entry** `upsertDataSourcePage`
- **Query data source entries** `queryDataSource`
- **Create page** `createPage`
- **Append page content** `appendPageContent`

### Data source ID

Notion data source that holds the entries this step reads or writes. Use @ to insert prior-node data.

Open the database in Notion, then use "Manage data sources" → "Copy data source ID". A database ID is a different value and is rejected. The database must be shared with the connection.

- Type: Text
- Required
- Placeholder: `1f0b8c2d-3e4a-4b5c-8d9e-0f1a2b3c4d5e`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create or update data source entry**, **Query data source entries**

### Filter

Notion filter object applied to the query, for example {"property": "Approval status", "select": {"equals": "Approved"}}. Empty returns all entries. Use @ to insert prior-node data.

Uses Notion's filter JSON exactly as documented for the data source query API. Combine conditions with and/or groups.

- Type: JSON
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Query data source entries**

### Sorts

Notion sorts array, for example [{"property": "Order number", "direction": "descending"}]. Empty keeps Notion's default order.

- Type: JSON
- Optional
- Shown when **Operation** is **Query data source entries**

### Page size

Entries fetched per run, 1 to 100. Scheduled runs continue from the cursor until every matching entry is processed, then start over from the top. Notion serves at most 10,000 entries per query; a query that matches more fails with a hint to narrow the filter.

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **Query data source entries**

### Match property

Data source property used to find an existing entry: a title, text, or number property. The entry is created when no match exists and updated when exactly one entry matches. Use @ to insert prior-node data.

Pick a property whose value is unique per entry, such as the order number, so repeated runs update the same entry instead of adding duplicates.

- Type: Text
- Required
- Placeholder: `Order number`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create or update data source entry**

### Properties

Flat object of Notion property names and values, for example {"Order number": "<code v-pre>{{orderNumber}}</code>", "Total": "<code v-pre>{{total}}</code>"}. Values are coerced to each property's actual Notion type. Use @ to insert prior-node data.

Property names must match the data source exactly, including spelling and case. A name that does not exist in the data source fails the step.

- Type: JSON
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create or update data source entry**

### Page ID

Notion page this step works with: the parent page for Create page, or the page written to for Append page content. Use @ to insert prior-node data.

Copy the ID from the page URL, the 32-character value at the end. The page must be shared with the connection.

- Type: Text
- Required
- Placeholder: `1f0b8c2d3e4a4b5c8d9e0f1a2b3c4d5e`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create page**, **Append page content**

### Title

Title of the new page, shown in Notion's sidebar and search. Use @ to insert prior-node data.

- Type: Text
- Required
- Placeholder: <code v-pre>Order {{orderNumber}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create page**

### Content format

How the content is turned into Notion blocks.

Plain text writes each line as a paragraph. Markdown renders headings (#, ##, ###), bulleted and numbered lists, to-dos (- [ ]), and callouts (>) as native Notion blocks; inline styling such as bold or links is not converted.

- Type: Dropdown
- Optional
- Default: `plainText`
- Shown when **Operation** is one of **Create page**, **Append page content**

Options:

- **Plain text** `plainText`
- **Markdown** `markdown`

### Content

Page content written as Notion blocks according to the content format. Required for Append page content, optional for Create page. Use @ to insert prior-node data.

With Plain text, each line becomes a paragraph and Markdown syntax is written out literally. Switch the content format to Markdown to render common block syntax.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create page**, **Append page content**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Record used to fill in Notion properties, title, and content |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Result | `object` | Notion operation result |

The output object contains `pageId`, `pageUrl` and `action` (`created` or `updated`) for **Create or update data source entry** and **Create page**, plus `matchedProperty` for the data source operation. **Append page content** returns `pageId`, `action` (`appended`) and `blockCount`. **Query data source entries** returns the entries under `records`, plus `count` and `hasMore`. Notion's raw response is not returned.

## Examples

### Track Shopware orders in a Notion database

Create a Notion database with the properties Order number (title), Customer (text), Total (number) and Status (status), share it with the integration, and copy its data source ID (open the database, then **Manage data sources → Copy data source ID**). Connect a **Shopware Event Trigger** listening for **Order placed** and configure:

| Parameter | Value |
| --- | --- |
| Operation | Create or update data source entry |
| Data source ID | `1f0b8c2d-3e4a-4b5c-8d9e-0f1a2b3c4d5e` |
| Match property | `Order number` |
| Properties | <code v-pre>{"Order number": "{{data.orderNumber}}", "Customer": "{{data.orderCustomer.email}}", "Total": "{{data.amountTotal}}", "Status": "New"}</code> |

Values are converted to each property's Notion type, so the string total lands in the number property. A later run with the same order number updates the same row.

### Add a note to a page

| Parameter | Value |
| --- | --- |
| Operation | Append page content |
| Page ID | `1f0b8c2d3e4a4b5c8d9e0f1a2b3c4d5e` |
| Content | <code v-pre>Order {{data.orderNumber}} was placed by {{data.orderCustomer.email}}</code> |

### Pull approved products from a Notion board

Start with a **Schedule Trigger** and configure:

| Parameter | Value |
| --- | --- |
| Operation | Query data source entries |
| Data source ID | `1f0b8c2d-3e4a-4b5c-8d9e-0f1a2b3c4d5e` |
| Filter | `{"property": "Approval status", "select": {"equals": "Approved"}}` |
| Sorts | `[{"property": "Last edited", "direction": "descending"}]` |
| Page size | `50` |

Each scheduled run continues where the previous one stopped until every matching entry has been read, then starts again from the top. Content written with **Create page** or **Append page content** is limited to 100 blocks per run.

## Related resources

- [Notion API reference](https://developers.notion.com/reference/intro)
- [Create a Notion integration](https://developers.notion.com/docs/create-a-notion-integration)
- [Notion request limits](https://developers.notion.com/reference/request-limits)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The requested resource was not found**: the database or page is not shared with the integration, or you pasted the database ID from the URL instead of the data source ID. Share the resource under **Connections** in Notion and copy the ID via **Manage data sources → Copy data source ID**.
- **The node configuration is invalid**: the node checks properties against the data source before writing. Common causes: a property name in **Properties** or **Match property** that does not match the data source exactly (including spelling and case), a property type the node cannot write (supported are title, text, number, select, multi-select, status, date, checkbox, URL, email and phone), a value that cannot be converted to the property's type, a match property that is not a title, text or number property or has an empty value, two or more entries sharing the match value, more than 100 blocks of content, or several input records reaching one write. A query that matches more than 10,000 entries also fails with this message, because Notion stops paginating there; narrow the **Filter**, for example to a creation-date window.
- **The connected service rejected the request**: Notion refused the request as invalid. Typical causes are a status value that does not exist as an option in the database (status options must be created in Notion first; select and multi-select options are created on first write), or a **Filter** or **Sorts** that does not follow Notion's query syntax.
- **Authentication failed for the connected service**: the integration token was regenerated or the integration was removed from the workspace. Paste the current token into the credential under **Integrations**.
- **The connected service is rate limiting requests**: Notion allows roughly three requests per second per integration, and each create-or-update costs about three requests. **Create or update data source entry** and **Query data source entries** wait and try again; **Create page** and **Append page content** are never retried, so a throttled attempt fails the step. Re-run the workflow.
- **Markdown appears as literal characters**: set **Content format** to **Markdown**. Headings, bulleted and numbered lists, to-dos and callouts are converted; bold, links and inline code stay literal.
- **A scheduled query starts from the top every run**: the run resumes where the previous one stopped only while the data source, filter and sorts stay the same. A filter built from per-run values such as <code v-pre>{{$now}}</code> restarts the query each time.
