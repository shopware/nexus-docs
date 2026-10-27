---
nav:
  title: "Box API"
  position: 4
---

# Box API

Use the Box API node to store and manage files in Box from a workflow. Typical Shopware uses are archiving an order document per order with the order number stamped as metadata, creating a shared link to hand to a customer or partner, and reacting to files or metadata that appear in Box.

Pick an **Operation**: **Upload file** uploads text content into a folder and can stamp metadata and create a shared link in the same step; **Create folder** creates a folder; **Create metadata** and **Update metadata** write metadata fields on a file; **Create shared link** creates a link for an existing file; **Get events** polls Box's event stream for uploads, moves and metadata changes; **Request JSON** calls any Box REST endpoint with a JSON body for cases the other operations do not cover. **Create metadata** on a file that already carries an instance of the template updates that instance instead of failing, so reruns are safe. **Upload file** never overwrites: a file with the same name in the folder makes the step fail.

:::info
**Node type:** `action.box-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **box** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **Box**.
2. Choose **Connect** to authorise Nexus with your Box account through Box's OAuth 2.0 flow.

After the credential is saved, the **Parent folder ID** dropdown in the node lists the folders the connected account can see.

## Operations

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **Create metadata** `createMetadata`
- **Update metadata** `updateMetadata`
- **Create shared link** `createSharedLink`
- **Get events** `getEvents`
- **Request JSON** `requestJSON`

## Node parameters

Configure this node with these parameters.

### Operation

Box operation to execute

- Type: Dropdown
- Required
- Default: `uploadFile`

Options:

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **Create metadata** `createMetadata`
- **Update metadata** `updateMetadata`
- **Create shared link** `createSharedLink`
- **Get events** `getEvents`
- **Request JSON** `requestJSON`

### Parent folder ID

Box parent folder ID for file uploads and folder creation. Leave this as 0 to use the account root folder.

- Type: Dropdown
- Optional
- Default: `0`
- Options are loaded from your connected account
- Shown when **Operation** is one of **Upload file**, **Create folder**

### Folder name

Name for a new Box folder

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create folder**

### File ID

Box file ID for metadata or shared-link operations. If omitted, the node can use input.data.file.id or input.data.id.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create metadata**, **Update metadata**, **Create shared link**

### Process every input item

Create metadata for every item in an input array. File ID and metadata expressions are resolved separately for each item.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Create metadata**

### File name

Name for an uploaded Box file

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Upload file**

### Content type

MIME type for uploaded text or binary content

- Type: Text
- Optional
- Default: `text/plain`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Upload file**

### Upload content

Text content to upload. Use workflow template expressions such as <code v-pre>{{data.orderNumber}}</code>.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Upload file**

### Metadata scope

Box metadata scope. Use global when enterprise templates are unavailable.

- Type: Text
- Optional
- Default: `global`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Upload file**, **Create metadata**, **Update metadata**

### Metadata template key

Box metadata template key for Nexus state and commerce correlation fields.

- Type: Text
- Optional
- Default: `properties`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Upload file**, **Create metadata**, **Update metadata**

### Metadata

Metadata fields to write to Box global.properties or the configured template

- Type: Key-value list
- Optional
- Shown when **Operation** is one of **Upload file**, **Create metadata**, **Update metadata**

### Shared link access

Optional shared-link access to create after upload or for an existing file

- Type: Dropdown
- Optional
- Shown when **Operation** is one of **Upload file**, **Create shared link**

Options:

- **Do not create** ``
- **Open** `open`
- **Company** `company`
- **Collaborators** `collaborators`

### Event stream position

Box Events stream_position. Leave empty to use the workflow watermark.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Get events**

### Event types

Box event types to include while polling Events

- Type: Multi-select
- Optional
- Default: `["ITEM_UPLOAD", "METADATA_INSTANCE_CREATE"]`
- Shown when **Operation** is **Get events**

Options:

- **File uploaded** `ITEM_UPLOAD`
- **File copied** `ITEM_COPY`
- **File moved** `ITEM_MOVE`
- **File renamed** `ITEM_RENAME`
- **File moved to trash** `ITEM_TRASH`
- **Metadata instance created** `METADATA_INSTANCE_CREATE`
- **Metadata instance updated** `METADATA_INSTANCE_UPDATE`
- **Metadata instance deleted** `METADATA_INSTANCE_DELETE`

### Max records

Maximum Box Events records to retrieve

- Type: Number
- Optional
- Default: `25`
- Shown when **Operation** is **Get events**

### Max pages

Maximum Events pages to read before stopping

- Type: Number
- Optional
- Default: `1`
- Shown when **Operation** is **Get events**

### HTTP method

HTTP method for a generic Box JSON request

- Type: Dropdown
- Optional
- Default: `GET`
- Shown when **Operation** is **Request JSON**

Options:

- **GET** `GET`
- **POST** `POST`
- **PUT** `PUT`
- **PATCH** `PATCH`
- **DELETE** `DELETE`

### Request path

Box REST path for requestJSON, for example /files/{id} or /folders/0/items

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Request JSON**

### Request body

JSON body for POST, PUT, and PATCH requestJSON calls. Leave empty for GET and DELETE.

- Type: JSON
- Optional
- Shown when **Operation** is **Request JSON**

### Response fields

Optional Box fields array to request on supported endpoints, such as ["id", "name", "shared_link"].

- Type: JSON
- Optional
- Shown when **Operation** is **Request JSON**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Box template interpolation and request payloads |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `any` | Box operation result, including file, metadata, shared link, event page, JSON response data, or per-item metadata results |

The output object contains the result of the operation: the uploaded file under `file` (with `metadata` and `sharedLink` when those were requested in the same step), the created folder under `folder`, `fileId` and `metadata` for the metadata operations, `fileId` and `file` for **Create shared link**, `items`, `nextStreamPosition` and `total` for **Get events**, the JSON response of a **Request JSON** call, or one metadata result per item when **Process every input item** is enabled. The shared link URL is at <code v-pre>{{file.shared_link.url}}</code> after **Create shared link**, and at <code v-pre>{{sharedLink.shared_link.url}}</code> after an **Upload file** step that created a link.

## Examples

### Archive an order document

Upload a generated order document to Box, stamp Shopware identity metadata, and return a shared link.

```json
{
  "operation": "uploadFile",
  "parentId": "0",
  "fileName": "sw-order-{{data.orderNumber}}.txt",
  "contentType": "text/plain",
  "content": "Order {{data.orderNumber}}",
  "metadataScope": "global",
  "metadataTemplateKey": "properties",
  "metadata": {
    "shopwareOrderNumber": "{{data.orderNumber}}"
  },
  "sharedLinkAccess": "company"
}
```

### Poll Box Events

Read uploaded-file and metadata events from the current workflow watermark.

```json
{
  "operation": "getEvents",
  "streamPosition": "{{meta.watermark}}",
  "eventTypes": [
    "ITEM_UPLOAD",
    "METADATA_INSTANCE_CREATE"
  ],
  "maxRecords": 25,
  "maxPages": 1
}
```

## Related resources

- [Box API reference](https://developer.box.com/reference/)
- [Box Events API](https://developer.box.com/reference/get-events/)
- [Box: OAuth 2.0 setup](https://developer.box.com/guides/authentication/oauth2/)
- [Box: metadata templates and instances](https://developer.box.com/guides/metadata/)
- [Box: shared links](https://developer.box.com/guides/shared-links/)
- [Box: API rate limits](https://developer.box.com/guides/api-calls/permissions-and-errors/rate-limits/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Authentication failed for the connected service**: the Box authorisation was revoked or has expired, or Box refused to renew it. Reconnect the credential under **Integrations**.
- **The node configuration is invalid**: most often **Upload file** used a **File name** that already exists in the target folder (Box reports `item_name_in_use`, which Nexus does not display). The node has no overwrite option, so make the name unique, for example <code v-pre>order-{{orderNumber}}.json</code>, or upload into a fresh folder. This also happens on a rerun after an upload whose metadata or shared link failed: the file itself was already stored. The same message covers a required field left empty, such as **File ID** or **Folder name**, and other requests Box refuses as invalid.
- **The requested resource was not found**: the **File ID** or **Parent folder ID** does not exist or is not visible to the connected Box account, or the **Metadata template key** does not exist under **Metadata scope** `enterprise`. Use scope `global` with template key `properties` for free-form key-value metadata.
- **Permission denied by the connected service**: the connected Box account may not write to that folder or file. Give the account editor access, or connect an account that has it.
- **The node failed unexpectedly**: a frequent cause is Box rate limiting, which this node does not recognise as such and does not slow down for. **Upload file**, **Create folder**, **Update metadata**, **Create shared link** and **Request JSON** run once, so a throttled attempt fails straight away; **Create metadata** and **Get events** are retried with a standard backoff. Reduce **Max records** and **Max pages** when polling events, and spread out bursts of uploads.
- **A shared link with Open access is refused**: your Box enterprise settings can forbid open links. Use `Company` or `Collaborators` for **Shared link access**.
- **Parent folder ID stays empty**: the credential is not connected yet. Connect Box first, or leave the value at `0` to use the account root folder.
