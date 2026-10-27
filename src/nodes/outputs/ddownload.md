---
nav:
  title: "DDownload"
  position: 1
---

# DDownload

Use the DDownload node to upload files to DDownload and share the hosted download link. It suits workflows that hand a generated file to someone outside your systems: an order export for a partner, a price list for a reseller, or a document you then post to Slack or email.

Pick an **Operation**: **Upload file** uploads content and returns its download link; **Get file info** looks up one or more files by file code; **List files** lists files in the account or in a folder; **Create folder** gets or creates a folder. Build the file content with a **Field Mapping** node before the upload.

:::info
**Node type:** `output.ddownload` · **Category:** Output
:::

## Credentials

:::info
This node uses a **ddownload** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In your DDownload account, open **Account**, then **Affiliate**, then **Settings**, and choose **Generate API Key**. The free affiliate account must be activated once before a key can be generated. Regenerating the key invalidates the previous one immediately.
2. In Nexus, open **Integrations** and select **DDownload**.
3. Enter the **API key** and choose **Test connection**.

The key has full account access, with no scopes and no expiry.

## Operations

- **Upload file** `uploadFile`
- **Get file info** `getFileInfo`
- **List files** `listFiles`
- **Create folder** `createFolder`

## Node parameters

Configure this node with these parameters.

### Operation

DDownload operation to execute

- Type: Dropdown
- Required
- Default: `uploadFile`

Options:

- **Upload file** `uploadFile`
- **Get file info** `getFileInfo`
- **List files** `listFiles`
- **Create folder** `createFolder`

### File Name

Name of the file to upload, including its extension. Uploaded files can be downloaded by anyone who has the link. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Upload file**

### Content

File content, as plain text or base64 per Content Encoding. The connector rejects anything over 4 MiB decoded, but the effective limit is lower: content arriving from an upstream node travels inside a 2 MiB workflow payload, so roughly 1.5 MiB of base64 is the real ceiling and larger content fails on the node that produced it. Content typed here directly is limited to roughly 400 KB, because it is stored with the workflow. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Required
- Shown when **Operation** is **Upload file**

### Content Encoding

How Content is encoded. Use base64 to upload binary data produced upstream.

- Type: Dropdown
- Optional
- Default: `text`
- Shown when **Operation** is **Upload file**

Options:

- **Text** `text`
- **Base64** `base64`

### Content Type

MIME type sent with the upload. Defaults to application/octet-stream; the placeholder shows an example.

- Type: Text
- Optional
- Default: `application/octet-stream`
- Placeholder: `application/json`
- Shown when **Operation** is **Upload file**

### Conflict Behavior

What happens when a file with the same name and size already exists in the account. Reuse returns the existing file and is the only retry-safe option, but it only converges the file's folder and visibility when the match is in the account root — a match already filed in another folder is returned untouched, because it may belong to a different workflow, so the step can succeed reporting a folder and visibility other than the ones configured. Duplicate and fail both run at most once, so a rate limit or network blip fails the step instead of retrying. DDownload never replaces files.

- Type: Dropdown
- Optional
- Default: `reuse`
- Shown when **Operation** is **Upload file**

Options:

- **Reuse existing** `reuse`
- **Upload duplicate** `duplicate`
- **Fail** `fail`

### Show in Public Listing

List the file publicly on the DDownload account profile. Regardless of this setting, anyone who has the download link can download the file.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Upload file**

### Folder ID

Numeric DDownload folder id. For upload, the target folder (empty keeps the file at the account root); for list, the folder to filter by (empty or 0 lists all files in the account). Use a Create Folder node output to obtain an id. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is one of **Upload file**, **List files**

### File Code

DDownload file code from a previous upload (the id in the download link). Multiple codes can be comma-separated. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Get file info**

### File Name Filter

Return only files whose name contains this text. Empty returns all files. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is **List files**

### Max Items

Maximum number of files to return across paged requests (capped at 1000).

- Type: Number
- Optional
- Default: `200`
- Shown when **Operation** is **List files**

### Folder Name

Name of the folder to get or create under the parent folder. An existing folder with the same name is reused. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Create folder**

### Parent Folder ID

Numeric id of the parent folder. Empty creates the folder at the account root. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is **Create folder**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for DDownload name, content, and id templating |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | DDownload operation result including download links |

**Upload file** returns `fileCode`, `name`, `size`, `downloadUrl`, `reused` (true when an existing file was returned instead of uploading), `public` and `folderId`. **Get file info** and **List files** return `items` with one entry per file (`fileCode`, `name`, `size`, `downloadUrl`, plus `status` for file info and `public` and `folderId` for listings) and `total`. **Create folder** returns `folderId`, `name` and `existed`. Download links always use the form `https://ddownload.com/{fileCode}`.

## Examples

### Upload an order export and post the link to Slack

Connect a **Shopware Event Trigger** listening for **Order placed**, build the export with a **Field Mapping** node that writes the order into a field called `exportJson`, and configure this node:

| Parameter | Value |
| --- | --- |
| Operation | Upload file |
| File Name | <code v-pre>order-{{data.orderNumber}}.json</code> |
| Content | <code v-pre>{{exportJson}}</code> |
| Content Encoding | Text |
| Content Type | `application/json` |
| Conflict Behavior | Reuse existing |
| Show in Public Listing | false |
| Folder ID | `12345` |

Then add a **Send Slack Message** node directly after this node with the message <code v-pre>Order export ready: {{downloadUrl}}</code>.

## Related resources

- [DDownload API documentation](https://ddownload.com/api)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Anyone with the link can download the file**: **Show in Public Listing** only controls whether the file appears on your public DDownload profile. It is not access control. Do not upload confidential content.
- **The upload returned an older file**: **Conflict Behavior** `Reuse existing` matches an existing file by exact name and byte size and returns it. Put the order or product number in the file name so different exports never share a name; if you must store new bytes under the same name and size, use `Upload duplicate`.
- **A rate limit or network blip failed the step**: with `Upload duplicate` or `Fail` the upload runs at most once and is never retried, because a retry could create a second copy. `Reuse existing` is the only retry-safe mode. DDownload allows roughly 3 to 4 requests per second per key.
- **Invalid key**: the key is wrong or was regenerated. Update the credential under Integrations.
- **List files returns the whole account**: an empty **Folder ID** or `0` lists every file in the account, not the root folder. Set the numeric folder ID to filter.
- **Content too large**: content from an upstream node is limited to roughly 1.5 MiB; content typed directly into the node to about 400 KB.
