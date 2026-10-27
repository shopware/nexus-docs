---
nav:
  title: "Microsoft OneDrive"
  position: 6
---

# Microsoft OneDrive

Use the Microsoft OneDrive node to store files in a OneDrive or SharePoint document library from a workflow. Typical Shopware uses are archiving an order document per order, writing a fulfilment worksheet the warehouse opens in Excel, or publishing a product or customer data sheet to a team site.

Pick an **Operation**: **Upload file** writes a file into a folder (missing parent folders are created on the way); **Create folder** gets or creates a folder; **Create sharing link** returns a view or edit link for a file or folder; **List folder children** reads a folder's entries. The drive is bound to the credential, so the node only carries paths inside that drive.

:::info
**Node type:** `output.onedrive` · **Category:** Output
:::

## Credentials

:::info
This node uses a **onedrive** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In your Microsoft Entra admin center, register an application, grant it the Microsoft Graph **application** permission `Files.ReadWrite.All` with admin consent, and create a client secret.
2. In Nexus, open **Integrations** and select **Microsoft OneDrive**.
3. Enter the **Directory (tenant) ID** (or the tenant domain such as `contoso.onmicrosoft.com`), the **Application (client) ID** and the **Client secret** value.
4. Enter a **User account (for drive lookup)**, choose **Load drives** and pick the **Target drive**, or paste the **Drive ID** directly. The account is only used to look up drives and is not stored. If no drives are listed, admin consent for `Files.ReadWrite.All` has not been granted, or the account has no drives the app can reach.
5. Choose **Test Connection** to verify the credentials and that the drive is reachable.

`Files.ReadWrite.All` grants access to every drive in your tenant. Point the credential at a dedicated SharePoint or team drive so workflows only write where you intend. Personal (consumer) OneDrive accounts cannot be used.

## Operations

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **Create sharing link** `createSharingLink`
- **List folder children** `listChildren`

## Node parameters

Configure this node with these parameters.

### Operation

OneDrive operation to execute

- Type: Dropdown
- Required
- Default: `uploadFile`

Options:

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **Create sharing link** `createSharingLink`
- **List folder children** `listChildren`

### Folder Path

Forward-slash folder path without a leading slash. Parent folder for upload and create-folder, or the folder to read for list-children. Empty targets the drive root. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is one of **Upload file**, **Create folder**, **List folder children**

### File Name

Name of the file to create or replace, including its extension. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Upload file**

### Content

File content, as plain text or base64 per Content Encoding. Base64 from an upstream node is capped at 4 MiB decoded; content typed here directly is limited to roughly 400 KB, because it is stored with the workflow. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Required
- Shown when **Operation** is **Upload file**

### Content Encoding

How Content is encoded. Use JSON when the file is a JSON document built from mapped fields: values are escaped by the serializer and a field the mapping omitted is left out instead of written as an empty string. Use base64 to upload binary data produced upstream, and text for anything else.

- Type: Dropdown
- Optional
- Default: `text`
- Shown when **Operation** is **Upload file**

Options:

- **Text** `text`
- **JSON** `json`
- **Base64** `base64`

### Content Type

MIME type stored with the file. Defaults to application/octet-stream; the placeholder shows an example.

- Type: Text
- Optional
- Default: `application/octet-stream`
- Placeholder: `application/json`
- Shown when **Operation** is **Upload file**

### Conflict Behavior

What happens when a file already exists at the path. Replace is idempotent and retry-safe; rename and fail run at most once.

- Type: Dropdown
- Optional
- Default: `replace`
- Shown when **Operation** is **Upload file**

Options:

- **Replace** `replace`
- **Rename** `rename`
- **Fail** `fail`

### Folder Name

Name of the folder to get or create under Folder Path. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Create folder**

### Item Path

Full forward-slash path to the file or folder to share, without a leading slash. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is **Create sharing link**

### Link Type

Access the sharing link grants.

- Type: Dropdown
- Optional
- Default: `view`
- Shown when **Operation** is **Create sharing link**

Options:

- **View** `view`
- **Edit** `edit`

### Link Scope

Who can use the link. Organization requires sign-in to the tenant; anonymous works for anyone with the link and may be disabled by tenant policy.

- Type: Dropdown
- Optional
- Default: `organization`
- Shown when **Operation** is **Create sharing link**

Options:

- **Organization** `organization`
- **Anonymous** `anonymous`

### Max Items

Maximum number of entries to return across paged requests (capped at 1000).

- Type: Number
- Optional
- Default: `200`
- Shown when **Operation** is **List folder children**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for OneDrive path, name, and content templating |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | OneDrive operation result |

**Upload file** returns `id`, `name`, `webUrl`, `size` and `eTag` of the stored file. **Create folder** returns `id`, `name`, `webUrl` and `existed` (true when the folder was already there). **Create sharing link** returns `url`, `type` and `scope`. **List folder children** returns an array of entries with `id`, `name`, `isFolder`, `size`, `lastModifiedDateTime` and `webUrl`.

## Examples

### Archive every order as a JSON document

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`), map the order with a **Field Mapping** node that writes it into a field called `orderJson`, and configure:

| Parameter | Value |
| --- | --- |
| Operation | Upload file |
| Folder Path | <code v-pre>Orders/{{data.orderNumber}}</code> |
| File Name | <code v-pre>order-{{data.orderNumber}}.json</code> |
| Content | <code v-pre>{{orderJson}}</code> |
| Content Encoding | JSON |
| Content Type | `application/json` |
| Conflict Behavior | Replace |

Use **Content Encoding** `JSON` for JSON documents built from mapped fields: values are escaped correctly and a field the mapping omitted is left out instead of written as an empty string.

### Share the order folder with the team

| Parameter | Value |
| --- | --- |
| Operation | Create sharing link |
| Item Path | <code v-pre>Orders/{{data.orderNumber}}</code> |
| Link Type | View |
| Link Scope | Organization |

## Related resources

- [Microsoft Graph: Upload small files](https://learn.microsoft.com/en-us/graph/api/driveitem-put-content)
- [Microsoft Graph: Create a folder](https://learn.microsoft.com/en-us/graph/api/driveitem-post-children)
- [Microsoft Graph: Create a sharing link](https://learn.microsoft.com/en-us/graph/api/driveitem-createlink)
- [Microsoft Graph: List folder children](https://learn.microsoft.com/en-us/graph/api/driveitem-list-children)
- [Microsoft Entra: register an application](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app)
- [Microsoft Graph permissions reference: Files.ReadWrite.All](https://learn.microsoft.com/en-us/graph/permissions-reference#filesreadwriteall)
- [OneDrive: invalid file names and file types](https://support.microsoft.com/en-us/office/restrictions-and-limitations-in-onedrive-and-sharepoint-64883a5d-228e-48f5-b3d2-eb39e07630fa)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Authentication failed for the connected service**: Microsoft rejected the app registration. The **Directory (tenant) ID**, **Application (client) ID** or **Client secret** is wrong, or the secret has expired (Entra secrets expire after at most 24 months). Microsoft reports this as `invalid_client`, which Nexus does not display. Create a new secret and update the credential under **Integrations**.
- **The node received data it could not process**: a path segment or **File Name** breaks OneDrive's naming rules: it contains one of `" * : < > ? / \ |`, starts or ends with a space, ends with a period, starts with `~$`, is a reserved name, or is too long. Nexus does not say which segment, so check each part of **Folder Path**, **File Name** and **Item Path**. The same message appears when a <code v-pre>{{...}}</code> token in a path field did not resolve (the details name the field and the token) or when the content is larger than 4 MiB (for **Base64**, after decoding). Content from an upstream node is also bounded by the 2 MiB workflow payload limit, so in practice files must be well below 4 MiB.
- **The requested resource was not found**: the item in **Item Path**, or the folder that **List folder children** reads, does not exist in the drive bound to the credential. **Upload file** creates missing folders itself, so this message points at sharing-link and list steps. Check the path and the **Target drive** on the credential.
- **Permission denied by the connected service**: the app may not access the drive, for example because admin consent for `Files.ReadWrite.All` is missing. **Link Scope** `Anonymous` can also be refused when your tenant or site policy disables anonymous links; use `Organization`, or ask a SharePoint administrator to allow anonymous sharing on that site.
- **The connected service rejected the request**: most often **Conflict Behavior** `Fail` found an existing file with the same name. Use `Replace` when a rerun should overwrite the file.
- **The node configuration is invalid**: the drive is full, OneDrive flagged the file as malware, a required field such as **File Name** or **Item Path** is empty, or **Create folder** found a file at the folder's path.
- **The connected service is rate limiting requests**: Microsoft Graph throttles bursts. Nexus waits the time Graph asks for and retries, but only for operations that are safe to repeat: an upload with **Conflict Behavior** `Rename` or `Fail` runs once, because a retry could create a duplicate, so a throttled attempt fails straight away. If it happens often, spread the workflow's runs or reduce the number of files per run.
