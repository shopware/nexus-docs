---
nav:
  title: "Dropbox"
  position: 2
---

# Dropbox

Use the Dropbox node to store files in a Dropbox folder from a workflow and to share them by link. Typical Shopware uses are writing an order document per order for a fulfilment partner who works out of Dropbox, keeping a dated export per day, or creating a shared link to hand to a customer.

Pick an **Operation**: **Upload file** writes a file into a folder; **Create folder** creates a folder; **List folder** reads a folder's entries; **Create shared link** returns a link for a file or folder. All paths are inside the folder your Dropbox app has access to and must start with `/`, for example `/Orders/2026-08`. `/` on its own means the root of that folder and is accepted only by **List folder**.

:::info
**Node type:** `output.dropbox` · **Category:** Output
:::

## Credentials

:::info
This node uses a **dropbox** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In the Dropbox App Console, create a scoped app and grant it the `account_info.read`, `files.metadata.read`, `files.content.read`, `files.content.write` and `sharing.write` scopes.
2. Generate a refresh token for the app using the OAuth flow with `token_access_type=offline`.
3. In Nexus, open **Integrations** and select **Dropbox**.
4. Enter the **App key**, **App secret** and **Refresh token**, then choose **Test connection**.

Nexus uses the refresh token to obtain short-lived access tokens itself. The refresh token does not expire on its own, but revoking the app in Dropbox invalidates it for every workflow that uses the credential.

## Operations

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **List folder** `listFolder`
- **Create shared link** `createSharedLink`

## Node parameters

Configure this node with these parameters.

### Operation

Dropbox operation to execute

- Type: Dropdown
- Required
- Default: `uploadFile`

Options:

- **Upload file** `uploadFile`
- **Create folder** `createFolder`
- **List folder** `listFolder`
- **Create shared link** `createSharedLink`

### Path

Dropbox folder path, for example /Orders/2026-08. The value must name a file or folder inside the app folder; / alone is not a valid target. Supports <code v-pre>{{field}}</code> templating. Carriage returns, line feeds, and non-printable characters are rejected.

- Type: Text
- Required
- Shown when **Operation** is one of **Upload file**, **Create folder**, **List folder**, **Create shared link**

### File Name

File name including extension. Supports <code v-pre>{{field}}</code> templating. Carriage returns, line feeds, and non-printable characters are rejected.

- Type: Text
- Required
- Shown when **Operation** is **Upload file**

### Content

File content as text or base64 per Content Encoding. Content travels inside the workflow activity payload, which the platform caps at 2 MiB, so roughly 1.5 MiB of base64 is the effective limit. Supports <code v-pre>{{field}}</code> templating.

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

### Write Mode

How Dropbox handles an existing file. Add renames on conflict when Autorename is on; overwrite replaces the existing file.

- Type: Dropdown
- Optional
- Default: `add`
- Shown when **Operation** is **Upload file**

Options:

- **Add** `add`
- **Overwrite** `overwrite`

### Autorename

On a name conflict, let Dropbox pick a new name instead of failing.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Upload file**

### Max Items

Maximum number of entries to return across paged requests (capped at 1000).

- Type: Number
- Optional
- Default: `200`
- Shown when **Operation** is **List folder**

### Link Audience

Who can access the shared link.

- Type: Dropdown
- Required
- Default: `team`
- Shown when **Operation** is **Create shared link**

Options:

- **Public** `public`
- **Team** `team`

### Link Expires At

ISO-8601 UTC expiry. Expiry requires a paid Dropbox plan (Professional, Business, or above), and the step fails permanently if the account cannot set it. It never silently creates a non-expiring link.

- Type: Text
- Optional
- Placeholder: `2026-08-31T23:59:59Z`
- Shown when **Operation** is **Create shared link**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Dropbox path, name, and content templating |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Dropbox operation result |

**Upload file** returns `id`, `name`, `pathDisplay`, `pathLower`, `size`, `contentHash`, `rev`, `serverModified`, `requestedPath` and `renamed` (true when **Autorename** gave the file a different name). **Create folder** returns `id`, `name`, `pathDisplay` and `existed`. **List folder** returns `items`, `total` and `hasMore`. **Create shared link** returns `url`, `id`, `name`, `pathLower`, `resolvedAudience`, `expires` and `existed` (true when the item already had a matching link).

## Examples

### Store an order document for a fulfilment partner

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`), build the document with a **Field Mapping** node that writes it into a field called `orderJson`, and configure:

| Parameter | Value |
| --- | --- |
| Operation | Upload file |
| Path | <code v-pre>/Orders/{{data.orderNumber}}</code> |
| File Name | <code v-pre>order-{{data.orderNumber}}.json</code> |
| Content | <code v-pre>{{orderJson}}</code> |
| Content Encoding | Text |
| Write Mode | Overwrite |

### Create a link for the team

| Parameter | Value |
| --- | --- |
| Operation | Create shared link |
| Path | <code v-pre>/Orders/{{data.orderNumber}}</code> |
| Link Audience | Team |

## Related resources

- [Dropbox HTTP API documentation](https://www.dropbox.com/developers/documentation/http/documentation)
- [Dropbox App Console](https://www.dropbox.com/developers/apps)
- [Dropbox OAuth guide (refresh tokens, token_access_type=offline)](https://developers.dropbox.com/oauth-guide)
- [Dropbox: shared links](https://www.dropbox.com/developers/documentation/http/documentation#sharing-create_shared_link_with_settings)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node received data it could not process**: Nexus or Dropbox rejected the path, the file name or the content. Common causes: **Path** does not start with `/`, ends with a slash or contains an empty segment; a <code v-pre>{{...}}</code> token in **Path** or **File Name** did not resolve; Dropbox reports a malformed or disallowed name; **Write Mode** `Add` found an existing file and **Autorename** is off; or **Content** is larger than 1.5 MiB (for **Base64** the limit applies to the decoded bytes). Content from an upstream node also counts against the 2 MiB workflow payload limit, and Base64 makes data about a third larger, so binary files close to 1.5 MiB do not fit. Use `Overwrite` when a rerun should replace the file.
- **The node configuration is invalid**: **Path** is `/` for an operation other than **List folder**, a required field is empty, or Dropbox reports that the account is out of space or the folder is restricted.
- **The requested resource was not found**: the file or folder in **Path** does not exist inside the app folder. Check the path, including its case and the folder your Dropbox app has access to.
- **Authentication failed for the connected service**: Dropbox reports that the app is missing one of the required scopes, or that the access is otherwise not allowed for this user. Fix the scopes in the App Console, generate a new refresh token and update the credential under **Integrations**.
- **The connected service rejected the request**: either Dropbox refused the refresh token (the app was revoked, or the app key, app secret or refresh token is wrong; generate a new refresh token and update the credential), or, on **Create shared link**, Dropbox refused the link settings. **Link Expires At** needs a paid Dropbox plan (Professional, Business or above), and team policies can forbid the chosen **Link Audience**; leave **Link Expires At** empty on other plans. If Dropbox creates the link but with a different audience or without the expiry, the step fails with **The node received data it could not process** instead of passing on a link you did not ask for.
- **Permission denied by the connected service**: Dropbox reports that the app may not write to the target folder. Check the folder's sharing settings in Dropbox.
- **The connected service is rate limiting requests**: Dropbox throttled the app or user. Nexus waits as long as Dropbox asks and tries again, up to three attempts in total; this message appears when the last attempt is also throttled. **Upload file** with **Write Mode** `Add` and **Autorename** on runs only once, because a retry could create a second renamed copy, so a throttled attempt fails straight away. Spread runs out when a workflow fires many uploads at once.
