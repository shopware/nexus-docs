---
nav:
  title: "goQR / QR Server"
  position: 34
---

# goQR / QR Server

Use the goQR / QR Server node to generate a QR code image or decode an existing one through the public goQR API. It is a lightweight way to produce a QR code for a Shopware order tracking page, a product page printed on packaging, or a returns portal, without keeping any record at the vendor: the code is generated on request and returned to the workflow.

Pick an **Entity**. **QR code images** encodes the text or URL in **QR data** into an image with the size, format, error-correction level, colours and margins you choose. **Decoded QR codes** reads the content of an image you supply, either as base64 in **Image bytes (base64)** or as a public **Remote image URL**.

The goQR API is public and needs no credential, so this node has no credential setting. Because it is a shared public service, keep it for moderate volumes and put only merchant-controlled URLs into codes you print.

:::info
**Node type:** `action.goqr-api` · **Category:** Action
:::

## Node parameters

Configure this node with these parameters.

### Base URL

QR Server API base URL. Use https://api.qrserver.com/v1 for live calls.

- Type: Text
- Optional
- Default: `https://api.qrserver.com/v1`

### Entity

goQR stateless resource to operate on. QR Code Images generates codes, and Decoded QR Codes reads existing images.

- Type: Dropdown
- Required
- Default: `qrCodeImages`

Options:

- **QR code images** `qrCodeImages`
- **Decoded QR codes** `decodedQRCodes`

### QR data

Text or URL encoded into generated QR images. Use merchant-controlled URLs for printed assets.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **QR code images**

### Format

Generated image format. PNG is safest for downstream binary handling.

- Type: Dropdown
- Optional
- Default: `png`
- Shown when **Entity** is **QR code images**

Options:

- **PNG** `png`
- **JPEG** `jpeg`
- **GIF** `gif`
- **SVG** `svg`
- **EPS** `eps`

### Size (px)

Square QR image size in pixels.

- Type: Number
- Optional
- Default: `300`
- Shown when **Entity** is **QR code images**

### Error correction

QR error-correction level.

- Type: Dropdown
- Optional
- Default: `M`
- Shown when **Entity** is **QR code images**

Options:

- **Low** `L`
- **Medium** `M`
- **Quartile** `Q`
- **High** `H`

### Foreground colour

QR foreground colour.

- Type: Color
- Optional
- Shown when **Entity** is **QR code images**

### Background colour

QR background colour.

- Type: Color
- Optional
- Shown when **Entity** is **QR code images**

### Margin

Outer margin passed to QR Server before rendering the QR image.

- Type: Number
- Optional
- Default: `0`
- Shown when **Entity** is **QR code images**

### Quiet zone

Quiet-zone size around the QR image.

- Type: Number
- Optional
- Default: `1`
- Shown when **Entity** is **QR code images**

### Image bytes (base64)

Base64-encoded QR image to decode.

- Type: Code / template
- Optional
- Shown when **Entity** is **Decoded QR codes**

### Remote image URL

Public image URL to decode when image bytes are not available.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Decoded QR codes**

### Content type

Image MIME type for multipart decode requests.

- Type: Text
- Optional
- Default: `image/png`
- Shown when **Entity** is **Decoded QR codes**

### File name

Filename sent with multipart decode requests.

- Type: Text
- Optional
- Default: `qr.png`
- Shown when **Entity** is **Decoded QR codes**

### Metadata

Optional workflow metadata retained with generated asset or decode output.

- Type: JSON
- Optional
- Default: `{"description": "goQR"}`

### Timeout (ms)

Per-request timeout for QR generation or decode.

- Type: Number
- Optional
- Default: `10000`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Workflow data used to build the QR payload or decode request. |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Generated QR binary metadata or decoded QR symbol data from goQR. |

For **QR code images** the output object contains `dataUri` (the image as a data URI), `base64`, `contentType`, `sizeBytes`, `format` and `payloadHash`, ready to be embedded in an email or passed to a storage node. For **Decoded QR codes** the output contains the decoded text as `data`, plus `type`, `seq` and all decoded `symbols`; the step fails when the image could not be read.

## Examples

### QR code for the order tracking page

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`) to this node and configure:

| Parameter | Value |
| --- | --- |
| Entity | QR code images |
| QR data | <code v-pre>https://shop.example.com/account/order/{{data.deepLinkCode}}</code> |
| Format | PNG |
| Size (px) | `300` |
| Error correction | High |
| Quiet zone | `2` |

Reference the image as <code v-pre>{{dataUri}}</code> in the node directly after this one, for example as an image source in an email or as the content written to a storage node. **High** error correction keeps the code readable when part of it is covered or smudged on packaging.

### Decode a QR code from a public image

| Parameter | Value |
| --- | --- |
| Entity | Decoded QR codes |
| Remote image URL | <code v-pre>{{data.imageUrl}}</code> |

## Related resources

- [goQR API docs](https://goqr.me/api/)
- [goQR API: create-qr-code](https://goqr.me/api/doc/create-qr-code/)
- [goQR API: read-qr-code](https://goqr.me/api/doc/read-qr-code/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: for **Decoded QR codes**, QR Server could not read the image. Supply a sharp, uncropped QR image, or a public image URL it can download; private or login-protected URLs do not work. For **QR code images**, a setting is outside what goQR accepts, for example an empty **QR data**, a **Size (px)** outside 10 to 1000, or a colour that is not a hex value or an R-G-B triplet.
- **The connected service is rate limiting requests**: the public QR Server API is shared and throttles bursts. Nexus waits and tries again up to three times in total; if the limit persists, the step fails. Add a **Delay** node or generate codes in smaller batches.
- **The connected service did not respond in time**: the request took longer than **Timeout (ms)**. Raise the timeout, or reduce the size and use PNG, which is the safest format for later processing.
- **The connected service is currently unavailable**: QR Server returned a server error on every attempt. Run the workflow again later.
- **The image is too small to scan**: raise **Size (px)** (up to 1000) or increase **Quiet zone**; a tight margin is a common cause of scanning failures on printed labels.
- **Printed code points at the wrong page**: the code is static and cannot be changed after it is generated. Encode a URL you control and can redirect, and check the value of **QR data** in a test run before printing.
