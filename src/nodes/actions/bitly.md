---
nav:
  title: "Bitly API"
  position: 3
---

# Bitly API

Use the Bitly API node to create and manage Bitly short links and QR codes and to read click and engagement metrics from a workflow. In a Shopware context it is useful for links that have to be short or trackable: an order tracking link for an SMS or a printed delivery note, a campaign link for a product or category, or a QR code for packaging, with the click summary read back later for reporting.

Pick an **Operation**. **Create Bitlink** and **Shorten URL** turn a long URL into a short link; **Get Bitlink**, **Update Bitlink**, **Archive Bitlink** and **Delete Bitlink** manage an existing link by its **Bitlink ID**; **Get clicks summary** and **Get engagements summary** return metrics for it; **Create QR code** and **Get QR code image** produce a QR code; **Create custom Bitlink** attaches a branded back-half; and the user, group and domain operations read account metadata. Every operation uses individual fields, so there is no raw request body to write.

:::info
**Node type:** `action.bitly-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **bitly** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Bitly, generate a generic access token under **Settings → API**. It is shown once; copy it.
2. In Nexus, open **Integrations** and select **Bitly**.
3. Paste the token into **Access token**. Optionally set **Group GUID (optional)**, the default group new links are created in, and **Short domain (optional)**, for example `bit.ly` or your branded domain.
4. Use **Test connection** to verify the token before saving.

The node's **Group GUID** and **Short Domain** fields override these defaults per node. If you do not know your group GUID, run the **List groups** operation once after saving the credential.

## Operations

- **Get user** `getUser`
- **List groups** `listGroups`
- **Get group** `getGroup`
- **List branded short domains** `listBrandedShortDomains`
- **Create Bitlink** `createBitlink`
- **Shorten URL** `shortenUrl`
- **Get Bitlink** `getBitlink`
- **List group Bitlinks** `listGroupBitlinks`
- **Update Bitlink** `updateBitlink`
- **Archive Bitlink** `archiveBitlink`
- **Delete Bitlink** `deleteBitlink`
- **Get clicks summary** `getClicksSummary`
- **Get engagements summary** `getEngagementsSummary`
- **Create QR code** `createQRCode`
- **Get QR code image** `getQRCodeImage`
- **Create custom Bitlink** `createCustomBitlink`

## Node parameters

Configure this node with these parameters.

### Operation

Bitly operation to execute.

Fill in the fields shown for the selected operation. See the [Bitly API reference](https://dev.bitly.com/api-reference/#createBitlink) for what each operation expects.

- Type: Dropdown
- Required
- Default: `createBitlink`

Options:

- **Get user** `getUser`
- **List groups** `listGroups`
- **Get group** `getGroup`
- **List branded short domains** `listBrandedShortDomains`
- **Create Bitlink** `createBitlink`
- **Shorten URL** `shortenUrl`
- **Get Bitlink** `getBitlink`
- **List group Bitlinks** `listGroupBitlinks`
- **Update Bitlink** `updateBitlink`
- **Archive Bitlink** `archiveBitlink`
- **Delete Bitlink** `deleteBitlink`
- **Get clicks summary** `getClicksSummary`
- **Get engagements summary** `getEngagementsSummary`
- **Create QR code** `createQRCode`
- **Get QR code image** `getQRCodeImage`
- **Create custom Bitlink** `createCustomBitlink`

### Bitlink ID

Bitlink identifier such as bit.ly/abc123 for get, update, archive, delete, or metrics operations.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Get Bitlink**, **Update Bitlink**, **Archive Bitlink**, **Delete Bitlink**, **Get clicks summary**, **Get engagements summary**

### Long URL

Long destination URL used when not providing a payload object.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create Bitlink**, **Shorten URL**

### Redirect Long URL

Optional new destination for an existing Bitlink. Leave blank to keep the current redirect; repointing a link can consume Bitly redirect and plan limits.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update Bitlink**

### Title

Optional Bitlink or QR title used when not provided in payload.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create Bitlink**, **Update Bitlink**, **Create QR code**

### Tags

Optional JSON array of Bitly tags, typically including mapping, campaign, and Shopware identifiers.

- Type: JSON
- Optional
- Default: `[]`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create Bitlink**, **Update Bitlink**

### Group GUID

Bitly group GUID for group reads, Bitlink creation, and QR operations.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Get group**, **List group Bitlinks**, **Create Bitlink**, **Shorten URL**, **Create QR code**

### Short Domain

Bitly short domain such as bit.ly or a branded short domain for create/custom operations.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create Bitlink**, **Shorten URL**, **Create custom Bitlink**

### QR Destination

Destination Bitlink or URL for QR-code creation when it is not included in payload.destination.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create QR code**

### Custom Bitlink ID

Bitlink ID to attach a custom back-half to, for example bit.ly/abc123.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create custom Bitlink**

### QR Code ID

QR code id (qrcode_id) returned by Create QR code, used to fetch the rendered image. When a Create QR code node runs directly before this node, reference its result as <code v-pre>{{id}}</code>.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Get QR code image**

### Custom Back-half

Custom back-half keyword for branded Bitlinks.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create custom Bitlink**

### Archived

Archive flag for lifecycle operations. Archive defaults to true for archiveBitlink.

- Type: Toggle
- Optional
- Shown when **Operation** is one of **Archive Bitlink**, **Update Bitlink**

### Page Size

Bitly page size for group, domain, and Bitlink list operations.

- Type: Number
- Optional
- Default: `50`
- Shown when **Operation** is one of **List groups**, **List branded short domains**, **List group Bitlinks**

### Limit

Maximum number of Bitly records to return.

- Type: Number
- Optional
- Shown when **Operation** is one of **List groups**, **List branded short domains**, **List group Bitlinks**

### Maximum Pages

Maximum Bitly pages to fetch while following pagination.

- Type: Number
- Optional
- Default: `1`
- Shown when **Operation** is one of **List groups**, **List branded short domains**, **List group Bitlinks**

### Search After Cursor

Bitly search_after cursor for continuing group Bitlink pagination.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List group Bitlinks**

### Metric Unit

Aggregation unit for Bitly click or engagement summaries.

- Type: Dropdown
- Optional
- Default: `day`
- Shown when **Operation** is one of **Get clicks summary**, **Get engagements summary**

Options:

- **Minute** `minute`
- **Hour** `hour`
- **Day** `day`
- **Week** `week`
- **Month** `month`

### Metric Window

Number of metric units to request.

- Type: Number
- Optional
- Default: `7`
- Shown when **Operation** is one of **Get clicks summary**, **Get engagements summary**

### Unit Reference

Optional ISO timestamp that anchors the metric window. Leave blank for Bitly's default reference time.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Get clicks summary**, **Get engagements summary**

### Query Parameters

Optional raw query parameters for Bitly list and metric operations.

- Type: Key-value list
- Optional
- Shown when **Operation** is one of **List groups**, **List branded short domains**, **List group Bitlinks**, **Get clicks summary**, **Get engagements summary**

### Retry Transient Write Failures

Opt in only when the workflow can tolerate duplicate creates or repeated plan usage after ambiguous transient failures.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Create Bitlink**, **Shorten URL**, **Update Bitlink**, **Archive Bitlink**, **Delete Bitlink**, **Create QR code**, **Create custom Bitlink**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Shopware URL, campaign, lifecycle, or metric context. |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Bitly API response. |

The output object contains Bitly's response as-is. **Create Bitlink** and **Shorten URL** return the new link, including `link` (the short URL) and `id` (the Bitlink ID, such as `bit.ly/abc123`, which the other operations take). **Get clicks summary** returns `total_clicks` for the requested window, and list operations return the collected records.

## Examples

### Short link to the order tracking page

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`) to this node and configure:

| Parameter | Value |
| --- | --- |
| Operation | Create Bitlink |
| Long URL | <code v-pre>https://shop.example.com/account/order/{{data.deepLinkCode}}</code> |
| Title | <code v-pre>Order {{data.orderNumber}}</code> |
| Tags | `["shopware", "order-tracking"]` |

Put a Send Slack Message or email node directly after this node and reference the short link as <code v-pre>{{link}}</code>.

### Read the last seven days of clicks

| Parameter | Value |
| --- | --- |
| Operation | Get clicks summary |
| Bitlink ID | `bit.ly/abc123` |
| Metric Unit | Day |
| Metric Window | `7` |

## Related resources

- [Bitly API reference](https://dev.bitly.com/api-reference/)
- [Bitly: Authentication](https://dev.bitly.com/docs/getting-started/authentication/)
- [Bitly: Rate limits](https://dev.bitly.com/docs/getting-started/rate-limits/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **A create failed but the link exists in Bitly**: operations that create or change something run once, because Bitly may already have accepted the request when a timeout or server error comes back. Depending on how the request failed, the run shows **The connected service did not respond in time**, **The connected service is currently unavailable** or, for a dropped connection, **The node failed unexpectedly**. Check Bitly before running the workflow again. Turning on **Retry Transient Write Failures** retries such failures but can create duplicate links or QR codes and consume plan allowance; enable it only where duplicates are harmless.
- **The node configuration is invalid**: the step stopped before calling Bitly. The most common cause is a missing group: **Create Bitlink**, **Create QR code** and other group-scoped operations need **Group GUID** on the node or **Group GUID (optional)** on the credential, and the run details say so. **Tags** must be a JSON array of strings, for example `["launch", "sku-1001"]`.
- **Permission denied by the connected service**: tags, a branded domain or a custom back-half depend on the Bitly plan and the group's settings. A plain `bit.ly` link may succeed where the branded request is refused; Nexus does not fall back to `bit.ly` silently.
- **The connected service is rate limiting requests**: either Bitly is throttling requests, or the monthly usage limit of your Bitly plan is used up (Bitly reports the API usage limit as exceeded). Read operations wait as long as Bitly asks and try again, except when the monthly limit is used up. Operations that create or change something are not retried unless **Retry Transient Write Failures** is on.
- **Authentication failed for the connected service**: the access token was revoked or regenerated. Update the credential under **Integrations**.
- **Shorten URL ignores Title and Tags**: Bitly's shorten endpoint accepts only the URL, group and domain. Use **Create Bitlink** when you need a title or tags.
