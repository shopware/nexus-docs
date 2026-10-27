---
nav:
  title: "Gofile"
  position: 3
---

# Gofile

Use the Gofile node to upload files to Gofile and hand out the download link. It suits workflows that deliver a file to a customer or supplier: a digital-goods package after a paid order, a catalogue or manual to publish, or a stock snapshot a supplier downloads.

Pick an **Operation**: **Upload file** uploads whatever arrives on the **Data** input as one file; **Delete content** removes a file or folder permanently; **Update attributes** changes any combination of **Expiry**, **Password**, **Public**, **Name**, **Description** and **Tags** on a file or folder in one step, which is how a public link becomes time-limited or protected (fields left empty keep their current value; Gofile takes one attribute per call, so if one call fails, the attributes before it stay applied and the step's details list which ones were and were not applied); **Create folder** creates a folder under a parent. Shape the file body with a **Field Mapping** node before the upload; the destination folder, file name and content type are node settings.

:::info
**Node type:** `output.gofile` · **Category:** Output
:::

## Credentials

:::info
This node uses a **gofile** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **Gofile**.
2. Enter the **API token** from your Gofile account page. Gofile has no scopes: the token carries whatever your account tier allows.
3. Optionally set a **Default folder ID (optional)**. Uploads land there when a node leaves its **Folder ID** empty. Use the folder's Gofile ID (a UUID such as `4472b4fc-43c2-4360-a1b1-2c3d4e5f6a7b`), not its name.
4. Optionally set an **Upload URL (optional)** to a regional Gofile upload host. Leave it blank to use Gofile's default.
5. Choose **Test connection**. The test proves the token resolves an account; whether your account tier may upload into a folder is only known on the first upload.

## Operations

- **Upload file** `uploadFile`
- **Delete content** `deleteContent`
- **Update attributes** `updateContent`
- **Create folder** `createFolder`

## Node parameters

Configure this node with these parameters.

### Operation

Gofile operation to execute

- Type: Dropdown
- Required
- Default: `uploadFile`

Options:

- **Upload file** `uploadFile`
- **Delete content** `deleteContent`
- **Update attributes** `updateContent`
- **Create folder** `createFolder`

### Folder ID

Destination folder's Gofile ID, not its name (e.g. '4472b4fc-43c2-4360-a1b1-2c3d4e5f6a7b'). Copy it from the folder's page in Gofile. For Create folder this is the parent the new folder is made in. Leave empty to use the Root Folder ID on the Gofile credential, or the account root when the credential has none.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Upload file**, **Create folder**
- Validated against pattern `^(?:.*\{\{.*\}\}.*|[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12})$`

### File Name

Name for the uploaded file (e.g., 'order-<code v-pre>{{orderNumber}}</code>.json'). Use '<code v-pre>{{$now:date}}</code>' for the run date (also 'time', 'datetime', 'iso', 'unix', all UTC) — a scheduled run has no order or product to name the file after, and Gofile never overwrites, so an undated name repeats every day. Leave empty to generate a unique name, whose extension follows the content type.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Upload file**

### Content Type

MIME type of the uploaded file, e.g. 'application/json' or 'text/csv'. It also decides the extension of a generated file name. Supports '<code v-pre>{{...}}</code>' values so a workflow can upload whatever type the previous step produced.

- Type: Text
- Optional
- Default: `application/json`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Upload file**
- Validated against pattern `^(?:.*\{\{.*\}\}.*|[a-zA-Z0-9][a-zA-Z0-9!#$&^_.+-]*\/[a-zA-Z0-9][a-zA-Z0-9!#$&^_.+-]*(?:\s*;.*)?)$`

### Content ID

Gofile ID of the file or folder to act on — the 'id' an upload returns, not the download-page code. Delete removes it permanently; Gofile has no undo and no trash.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Delete content**, **Update attributes**
- Validated against pattern `^(?:.*\{\{.*\}\}.*|[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12})$`

### Folder Name

Name for the new folder (e.g., 'orders-<code v-pre>{{orderDate}}</code>'). Gofile allows duplicate names, so creating the same folder twice makes two folders.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create folder**

### Expiry

Unix timestamp in seconds at which the content expires. Set any combination of the attributes below; each one is applied in its own Gofile call, and an empty field leaves that attribute unchanged.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update attributes**

### Password

Password visitors must enter on the download page. Leave empty to keep the current value.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update attributes**

### Public

'true' or 'false'. Leave empty to keep the current value.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update attributes**

### Name

New name for the file or folder. Leave empty to keep the current value.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update attributes**

### Description

Description shown on the download page. Leave empty to keep the current value.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update attributes**

### Tags

Comma-separated list of tags. Leave empty to keep the current value.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update attributes**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Data | `any` | Data to upload as a file |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Result | `object` | Operation result: upload metadata with content ID and download page, the created folder, the updated content, or the delete outcome |

For **Upload file** the output object contains the Gofile content metadata: `id`, `name`, `parentFolder`, `size`, `md5`, `mimetype`, `downloadPage` and `createTime`. Use `downloadPage` in the next node to share the link and `id` to delete or update the file. **Create folder** and **Update attributes** return the folder or content object (`id`, `name`, `parentFolder`, `type`, `code`, `createTime`), and **Delete content** returns `id`, `deleted` and `status`.

## Examples

### Share a daily stock snapshot with a supplier

Start with a **Schedule Trigger**, read the products with a **Shopware API Call** node and reshape them with a **Field Mapping** node, then connect the result to this node:

| Parameter | Value |
| --- | --- |
| Operation | Upload file |
| Folder ID | `4472b4fc-43c2-4360-a1b1-2c3d4e5f6a7b` |
| File Name | <code v-pre>stock-{{$now:date}}.csv</code> |
| Content Type | `text/csv` |

A scheduled run has no order or product to name the file after, so use <code v-pre>{{$now:date}}</code> for the run date. Gofile never overwrites, so an undated name repeats every day.

### Protect the link and let it expire

Add a second Gofile node directly after the upload node:

| Parameter | Value |
| --- | --- |
| Operation | Update attributes |
| Content ID | <code v-pre>{{id}}</code> |
| Expiry | `1798761600` (1 January 2027, 00:00 UTC, as a Unix timestamp in seconds) |
| Password | `supplier-2026` |

## Related resources

- [Gofile API documentation](https://gofile.io/api)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: **Folder ID**, **Content ID** or the credential's **Default folder ID (optional)** is not a Gofile ID. Use the UUID from the item's page in Gofile, not its name or its download-page code. The same message appears when **Create folder** has no parent folder (set **Folder ID** or a default folder on the credential), when **Update attributes** has no attribute set, or when **Public** is not `true` or `false` or **Expiry** is not a Unix timestamp in seconds.
- **The node received data it could not process**: nothing arrived on the **Data** input (for example an upstream read that found no records), so the node refuses to publish an empty file; a <code v-pre>{{...}}</code> token in **File Name** or an attribute field did not resolve (the step fails instead of uploading `catalogue-.json`); or Gofile rejected the request or could not find the folder or content, for example an ID that has the right shape but belongs to another account. Check the field names, or use a <code v-pre>{{$now:...}}</code> token for scheduled runs.
- **Gofile restricts this operation to paid accounts. The credential is valid, but the account tier does not allow it.**: Gofile gates some content operations, such as setting a password or expiry, to paid tiers. Until an **Update attributes** step has succeeded, treat the download page as a secret: anyone holding the link can download the file.
- **Authentication failed for the connected service**: Gofile rejected the API token because it was rotated or revoked. Gofile tokens cannot be refreshed; create a new token and update the credential under **Integrations**. If Gofile answers with a forbidden response instead, the step shows **Permission denied by the connected service**; the fix is the same.
- **The connected service is rate limiting requests**: Gofile throttled the account. **Upload file** waits as long as Gofile asks (up to 30 seconds) and tries again, up to three attempts in total. **Delete content** and **Update attributes** are retried as well; **Create folder** runs only once, because a retry could leave a second folder behind.
- **Two folders with the same name**: Gofile allows duplicate folder names, so running **Create folder** twice makes two folders. Create the folder once and reuse its ID.
- **Delete cannot be undone**: Gofile has no trash. Deleting content that is already gone is reported as success.
