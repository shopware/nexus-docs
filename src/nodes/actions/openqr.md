---
nav:
  title: "OpenQR API"
  position: 19
---

# OpenQR API

Use the OpenQR API node to create, update, list and read QR codes and folders in your OpenQR account from a workflow. Because OpenQR stores each code and can serve dynamic codes whose destination can be changed later, it suits QR codes that are printed: a code on the delivery note that opens the order tracking page, a code on packaging that opens the product page, or campaign codes organised into folders.

Pick an **Entity**, then an **Operation**. For **QR codes** you can list, get, create, update and upsert; for **Folders** you can list, create and update. Create and update take a **QR Name**, a **QR Code Type** such as URL or vCard, the JSON **Data** for that type and an optional JSON **Design**. **Upsert QR code** creates a code when **Upsert QR Code ID** is empty and updates the existing code when it is set. Deleting codes or folders is not available through the node.

:::info
**Node type:** `action.openqr-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **openqr** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In OpenQR, create or copy an API key in your account dashboard.
2. In Nexus, open **Integrations** and select **OpenQR**.
3. Enter the key in **API key** and use **Test connection**, which checks the key by listing your folders.

## Operations

- **List QR codes** `listQRCodes` (only when **Entity** is **QR codes**)
- **Get QR code** `getQRCode` (only when **Entity** is **QR codes**)
- **Create QR code** `createQRCode` (only when **Entity** is **QR codes**)
- **Update QR code** `updateQRCode` (only when **Entity** is **QR codes**)
- **Upsert QR code** `upsertQRCode` (only when **Entity** is **QR codes**)
- **List folders** `listFolders` (only when **Entity** is **Folders**)
- **Create folder** `createFolder` (only when **Entity** is **Folders**)
- **Update folder** `updateFolder` (only when **Entity** is **Folders**)

## Node parameters

Configure this node with these parameters.

### Entity

OpenQR resource to operate on

- Type: Dropdown
- Required
- Default: `qrCodes`

Options:

- **QR codes** `qrCodes`
- **Folders** `folders`

### Operation

OpenQR operation

- Type: Dropdown
- Required
- Default: `listQRCodes`

Options:

- **List QR codes** `listQRCodes` (only when **Entity** is **QR codes**)
- **Get QR code** `getQRCode` (only when **Entity** is **QR codes**)
- **Create QR code** `createQRCode` (only when **Entity** is **QR codes**)
- **Update QR code** `updateQRCode` (only when **Entity** is **QR codes**)
- **Upsert QR code** `upsertQRCode` (only when **Entity** is **QR codes**)
- **List folders** `listFolders` (only when **Entity** is **Folders**)
- **Create folder** `createFolder` (only when **Entity** is **Folders**)
- **Update folder** `updateFolder` (only when **Entity** is **Folders**)

### QR Name

OpenQR QR code name

- Type: Text
- Required
- Shown when **Operation** is one of **Create QR code**, **Update QR code**, **Upsert QR code** and **Entity** is **QR codes**

### QR Code Type

OpenQR QR code type

- Type: Dropdown
- Required
- Default: `url`
- Shown when **Operation** is one of **Create QR code**, **Update QR code** and **Entity** is **QR codes**

Options:

- **URL** `url`
- **URL static** `url_static`
- **Call** `call`
- **Call static** `call_static`
- **Email** `email`
- **Email static** `email_static`
- **SMS** `sms`
- **SMS static** `sms_static`
- **Event** `event`
- **Event static** `event_static`
- **Geo** `geo`
- **Geo static** `geo_static`
- **Text** `text`
- **Text static** `text_static`
- **Wi-Fi** `wifi`
- **Wi-Fi static** `wifi_static`
- **VCard** `vcard`
- **vCard static** `vcard_static`

### QR code ID

OpenQR QR code ID

- Type: Text
- Required
- Shown when **Operation** is one of **Get QR code**, **Update QR code** and **Entity** is **QR codes**

### Upsert QR Code ID

OpenQR QR code ID for upsert operation

- Type: Text
- Optional
- Shown when **Operation** is **Upsert QR code** and **Entity** is **QR codes**

### Folder ID

OpenQR folder ID

- Type: Text
- Required
- Shown when **Operation** is **Update folder** and **Entity** is **Folders**

### Folder Name

OpenQR folder name

- Type: Text
- Required
- Shown when **Operation** is one of **Create folder**, **Update folder** and **Entity** is **Folders**

### Data

JSON data for create, update,or upload operations. Supports expressions from previous nodes.

- Type: JSON
- Required
- Default: `{"url": "https://example.com"}`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create QR code**, **Update QR code**, **Upsert QR code** and **Entity** is **QR codes**

### Design

JSON data for the QR code design.

- Type: JSON
- Optional
- Default: `{"style": "square", "color": "#000000", "background": "#ffffff"}`
- Shown when **Operation** is one of **Create QR code**, **Update QR code**, **Upsert QR code** and **Entity** is **QR codes**

### Maximum cursor pages

Maximum number of OpenQR cursor pages to follow for list operations

- Type: Number
- Optional
- Default: `1`
- Shown when **Operation** is one of **List QR codes**, **List folders**

### Maximum records

Safety limit for OpenQR list results

- Type: Number
- Optional
- Default: `20`
- Shown when **Operation** is one of **List QR codes**, **List folders**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for OpenQR create, update, upsert, or list operations |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | OpenQR operation result, including QR ID and image URL when returned |

Read operations return the OpenQR records that were fetched; **Get QR code** returns one code and the list operations return the collected records across the requested pages. Write operations return OpenQR's response as-is, which includes the QR code ID and, when OpenQR returns it, the image URL.

## Examples

### Create a QR code for the order tracking page

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`) to this node and configure:

| Parameter | Value |
| --- | --- |
| Entity | QR codes |
| Operation | Create QR code |
| QR Name | <code v-pre>Order {{data.orderNumber}}</code> |
| QR Code Type | URL |
| Data | <code v-pre>{"url": "https://shop.example.com/account/order/{{data.deepLinkCode}}"}</code> |
| Design | `{"style": "square", "color": "#000000", "background": "#ffffff"}` |

Add `"qr_code_folder_id"` to **Data** to file the code in a folder; **List folders** returns the IDs.

### Group campaign codes in a folder

| Parameter | Value |
| --- | --- |
| Entity | Folders |
| Operation | Create folder |
| Folder Name | `Autumn packaging campaign` |

## Related resources

- [OpenQR API documentation](https://docs.openqr.io/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: the step stopped before calling OpenQR. Updates send the whole code, not a partial patch, so **QR Name**, **QR Code Type** and **Data** are all required even when only one of them changes. **Data** and **Design** must be JSON objects, and folder names must be at least five characters long. **Get QR code** and the update operations also need the ID of the code or folder.
- **The connected service rejected the request**: OpenQR refused the request body, most often because the keys in **Data** do not match the selected **QR Code Type**, for example `{"url": "..."}` for URL codes.
- **Authentication failed for the connected service**: the API key is invalid or was revoked. Create a new key in OpenQR and update the credential under **Integrations**.
- **The requested resource was not found**: the QR code ID or folder ID does not exist in this OpenQR account.
- **The connected service is rate limiting requests**: read operations (list and get) try again up to three times in total before the step fails. Create, update and upsert run once, so a rate limit fails the step immediately, as does a server error (shown as **The connected service is currently unavailable**); check OpenQR before running the workflow again so you do not create the code twice. For large scheduled batches, add a **Delay** node between records.
- **List returns only 20 records**: **Maximum records** defaults to 20 and **Maximum cursor pages** to 1. Raise both to read further pages.
