---
nav:
  title: "QRCode Monkey API"
  position: 24
---

# QRCode Monkey API

Use the QRCode Monkey API node to generate branded QR code images from a workflow. QRCode Monkey renders static codes with custom body and eye shapes, colours, gradients and an embedded logo, which makes it a good choice for printed material: a product page code on packaging, an order tracking code on the delivery note, or a code for a returns portal on an insert.

Pick an **Entity**: **Create custom QR code** renders a styled code on a solid background, **Create transparent QR code** renders one with a transparent background for placing on artwork. Both encode the value of **Data** and take an **Output format**, a **Size** and a JSON **Styling config** describing the look. The generated code is static: the encoded value cannot be changed afterwards, so put a URL you control into it.

:::info
**Node type:** `action.qrcode-monkey-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **qrcode-monkey** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In your QRCode Monkey API account, copy your API key.
2. In Nexus, open **Integrations** and select **QR Code Monkey**.
3. Enter the key in **API key** and use **Test connection** to verify it before saving.

## Node parameters

Configure this node with these parameters.

### Entity

QRCode Monkey entity to operate on.

- Type: Dropdown
- Required

Options:

- **Create custom QR code** `custom_qr_code`
- **Create transparent QR code** `transparent_qr_code`

### Data

URL, text, vCard, Wi-Fi payload, product number, or other data encoded into the QR image.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)

### Output format

Generated QR image format.

- Type: Dropdown
- Optional
- Default: `png`

Options:

- **PNG** `png`
- **SVG** `svg`
- **PDF** `pdf`
- **EPS** `eps`

### Size

Generated image size in pixels.

- Type: Number
- Optional
- Default: `600`

### Styling config

QRCode Monkey styling object with body/eye shapes, colours, gradients, and optional uploaded logo token.

- Type: JSON
- Optional
- Default: `{"body": "square", "bodyColor": "#111827", "bgColor": "#FFFFFF"}`

### Metadata

Correlation metadata copied into the generated asset output.

- Type: JSON
- Optional

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Shopware, campaign, document, or compliance context. |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Generated asset metadata or uploaded logo token returned by QRCode Monkey. |

The output object contains the generated image as `dataUri` and `base64`, with its `contentType`, `format` and `sizeBytes`, plus the values you put in **Metadata** under `metadata`, so a node directly after this one can match the asset to the order or product it belongs to.

## Examples

### Branded QR code for a product page

Connect a **Shopware Event Trigger** listening for **Product created or updated** (ID `product.written`) to this node and configure:

| Parameter | Value |
| --- | --- |
| Entity | Create custom QR code |
| Data | <code v-pre>https://shop.example.com/search?search={{data.productNumber}}</code> |
| Output format | SVG |
| Size | `1000` |
| Styling config | `{"body": "round", "eye": "frame1", "eyeBall": "ball1", "bodyColor": "#111827", "bgColor": "#FFFFFF"}` |
| Metadata | <code v-pre>{"productNumber": "{{data.productNumber}}"}</code> |

SVG keeps the code sharp at any print size. Pass the output to a storage node such as S3 Storage or Dropbox to keep the file with the product assets.

## Related resources

- [QRCode Monkey: QR Code API with logo and design](https://www.qrcode-monkey.com/qr-code-api-with-logo/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: the step stopped before calling QRCode Monkey, usually because **Data** is empty after the expressions are filled in, or because **Styling config** or **Metadata** is not valid JSON.
- **An unknown shape name is not rejected**: Nexus passes **Styling config** to QRCode Monkey unchanged, and QRCode Monkey does not reject shape names it does not know. It returns a code without the intended pattern, which may not scan. Use the option values listed in the vendor documentation (for example `round` or `rounded-pointed` for `body`) and scan a test code before printing.
- **Authentication failed for the connected service**: the API key is invalid or revoked. Check the key in your QRCode Monkey API account and update the credential under **Integrations**.
- **Permission denied by the connected service**: the key is valid but its subscription does not include this request.
- **The connected service is rate limiting requests**: the request limit of your QRCode Monkey API plan is used up. Generating a code runs once and is not retried, so the step fails. Spread scheduled batches with a **Delay** node or upgrade the plan.
- **The logo is missing from the code**: **Styling config** expects the token QRCode Monkey issues for an uploaded logo, not an image URL.
- **The printed code does not scan**: keep enough contrast between `bodyColor` and the background, use a size of at least 600 px or the SVG format for print, and test the code with a phone before producing packaging.
