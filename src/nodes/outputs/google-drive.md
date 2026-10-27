---
nav:
  title: "Google Drive"
  position: 4
---

# Google Drive

Use the Google Drive node to store files in a Google Drive shared drive from a workflow. Typical Shopware uses are archiving an order document per order, writing a fulfilment file the warehouse team opens from Drive, or publishing a product data sheet and sharing it by link.

Pick an **Operation**: **Upload file** writes a file into a folder; **Create folder** gets or creates a folder by name; **Create sharing link** grants link access to a file or folder and returns the link; **List files** reads a folder's entries. Drive addresses folders by ID, not by path, so **Folder ID** and **Parent Folder ID** take the ID from the folder's URL.

:::info
**Node type:** `output.google-drive` · **Category:** Output
:::

## Credentials

:::info
This node uses a **google-drive** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Google Cloud, create a service account, enable the **Google Drive API** for its project, and download the service account's JSON key.
2. In Google Drive, create a shared drive for Nexus and add the service account's email address as a member with **Content manager** (or **Manager** if workflows will create sharing links on folders). A Google Workspace edition that includes shared drives is required; personal Gmail accounts are not supported.
3. In Nexus, open **Integrations** and select **Google Drive**.
4. Paste the whole key file into **Service account key (JSON)**, enter the **Shared drive ID** (the last part of the shared drive's URL) and optionally a **Shared drive name (optional)**.
5. Choose **Test connection**. The test proves the key works and that the service account can reach the shared drive.

A service account has no storage of its own and cannot own Drive files, which is why the target must be a shared drive.

## Operations

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **Create sharing link** `createSharingLink`
- **List files** `listFiles`

## Node parameters

Configure this node with these parameters.

### Operation

Google Drive operation to execute

- Type: Dropdown
- Required
- Default: `uploadFile`

Options:

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **Create sharing link** `createSharingLink`
- **List files** `listFiles`

### File Name

File name including extension. Supports <code v-pre>{{field}}</code> templating. Carriage returns, line feeds, and non-printable characters are rejected.

- Type: Text
- Required
- Shown when **Operation** is **Upload file**

### Content

File content as text or base64 per Content Encoding. Content travels inside the workflow activity payload, which the platform caps at 2 MiB, so roughly 1.5 MiB of base64 is the effective limit when the value comes from an upstream node. Content typed directly into this field is stored with the workflow instead, where the practical limit is about 400 KB. Supports <code v-pre>{{field}}</code> templating.

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

MIME type stored with the file, for example application/json for a JSON document.

- Type: Text
- Optional
- Default: `application/octet-stream`
- Placeholder: `application/json`
- Shown when **Operation** is **Upload file**

### Write Mode

How an existing file with the same name in the target folder is handled. Overwrite replaces its content, which makes a retried step converge on one file. Add always creates a new file, so Google Drive keeps both copies and the step is never retried automatically.

- Type: Dropdown
- Optional
- Default: `overwrite`
- Shown when **Operation** is **Upload file**

Options:

- **Overwrite** `overwrite`
- **Add** `add`

### Folder ID

Target folder id inside the shared drive. Leave empty for the drive root. Copy the id from the folder URL in Google Drive. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is one of **Upload file**, **List files**

### Folder Name

Folder name to create, reusing an existing folder of the same name in the same parent. Supports <code v-pre>{{field}}</code> templating. Carriage returns, line feeds, and non-printable characters are rejected.

- Type: Text
- Required
- Shown when **Operation** is **Create folder**

### Parent Folder ID

Parent folder id for the new folder. Leave empty for the drive root. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is **Create folder**

### Item ID

File or folder id to share, usually <code v-pre>{{id}}</code> from an upstream Google Drive step. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Create sharing link**

### Link Role

What link recipients may do with the item.

- Type: Dropdown
- Optional
- Default: `reader`
- Shown when **Operation** is **Create sharing link**

Options:

- **Reader** `reader`
- **Commenter** `commenter`
- **Writer** `writer`

### Link Scope

Who the link works for. Anyone means anybody holding the link, without sign-in, and the link is not discoverable through search. Domain restricts it to one Google Workspace domain. A Google administrator can forbid either, in which case the step fails and reports the policy instead of quietly creating a narrower link.

- Type: Dropdown
- Optional
- Default: `anyone`
- Shown when **Operation** is **Create sharing link**

Options:

- **Anyone with the link** `anyone`
- **Everyone in a domain** `domain`

### Domain

Google Workspace domain the link is limited to, for example example.com.

- Type: Text
- Required
- Placeholder: `example.com`
- Shown when **Operation** is **Create sharing link** and **Link Scope** is **Everyone in a domain**

### Name Starts With

Only list items whose name starts with this value. Google Drive matches this as a prefix, not anywhere in the name.

- Type: Text
- Optional
- Shown when **Operation** is **List files**

### Max Items

Maximum number of items to return across paged requests (capped at 1000).

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **List files**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Google Drive name, folder, and content templating |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Google Drive operation result |

**Upload file** returns `id`, `name`, `mimeType`, `size`, `webViewLink`, `webContentLink`, `modifiedTime`, `folderId` and `overwritten`. **Create folder** returns `id`, `name`, `webViewLink` and `existed`. **Create sharing link** returns `url`, `role`, `scope`, `itemId`, `itemName` and `existed`, plus `contentUrl` for downloadable files and `domain` for domain-scoped links. **List files** returns `items` (each with `id`, `name`, `mimeType`, `isFolder`, `size`, `modifiedTime`, `webViewLink`), `total` and `hasMore`.

## Examples

### Store a product data sheet and share it

Connect a **Shopware Event Trigger** listening for **Product created or updated** (ID `product.written`), build the document with a **Field Mapping** node that writes it into a field called `productJson`, and configure this node:

| Parameter | Value |
| --- | --- |
| Operation | Upload file |
| File Name | <code v-pre>product-{{data.productNumber}}.json</code> |
| Content | <code v-pre>{{productJson}}</code> |
| Content Encoding | Text |
| Content Type | `application/json` |
| Write Mode | Overwrite |
| Folder ID | `1AbCdEfGhIjKlMnOpQrStUvWxYz` |

Then add a second Google Drive node directly after it:

| Parameter | Value |
| --- | --- |
| Operation | Create sharing link |
| Item ID | <code v-pre>{{id}}</code> |
| Link Role | Reader |
| Link Scope | Everyone in a domain |
| Domain | `example.com` |

## Related resources

- [Google Drive API: upload file data](https://developers.google.com/workspace/drive/api/guides/manage-uploads)
- [Google Drive API: create and populate folders](https://developers.google.com/workspace/drive/api/guides/folder)
- [Google Drive API: share files, folders and drives](https://developers.google.com/workspace/drive/api/guides/manage-sharing)
- [Google Drive API: implement shared drive support](https://developers.google.com/workspace/drive/api/guides/enable-shareddrives)
- [Google Cloud: create a service account and key](https://cloud.google.com/iam/docs/keys-create-delete)
- [Google Workspace: create a shared drive and add members](https://support.google.com/a/users/answer/9310156)
- [Google Drive API: usage limits](https://developers.google.com/workspace/drive/api/guides/limits)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: most often the target is not a shared drive. A service account has no storage of its own, so Drive refuses uploads into a personal My Drive (Google's reason is `storageQuotaExceeded`, which Nexus does not display). Check the **Shared drive ID** on the credential. The same message covers a shared drive or folder that has reached Google's item limits, or a folder hierarchy that is too deep.
- **The requested resource was not found**: the folder or item exists, but the service account is not a member of the shared drive that holds it, or the ID belongs to another drive. Add the service account's email address to the shared drive.
- **Permission denied by the connected service**: the service account's role on the shared drive is too low (it needs **Content manager**, or **Manager** to share folders), or a Workspace administrator policy blocks the request, for example anyone-with-link sharing or sharing outside the domain. Nexus does not name the policy; try a narrower **Link Scope** or ask the administrator.
- **Authentication failed for the connected service**: Google rejected the service-account key. The key was deleted, disabled or rotated, expired under an organisation policy, belongs to a project without the Google Drive API enabled, or the clocks disagree. Create a new key and update the credential under **Integrations**.
- **The node received data it could not process**: **Content** is larger than 1.5 MiB (for **Base64**, after decoding; content from an upstream node is also bounded by the 2 MiB workflow payload limit), the content is not valid Base64, a <code v-pre>{{...}}</code> token in a name or path field did not resolve, or **File Name** contains `/`.
- **The connected service is rate limiting requests**: Google allows about three sustained write requests per second per account and often reports throttling as a forbidden response; Nexus recognises it as a rate limit and retries with backoff. **Upload file** with **Write Mode** `Add` runs only once, because a retry would create a second copy, so a throttled attempt fails straight away. Spread runs out if it happens often.
- **Two files with the same name**: **Write Mode** `Add` always creates a new file, and Drive allows duplicate names in a folder. Use `Overwrite`, which replaces the existing file with that name and is safe to retry.
