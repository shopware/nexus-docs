---
nav:
  title: "Akeneo API"
  position: 2
---

# Akeneo API

Use the Akeneo API node to read and write Akeneo PIM products, attributes, families and categories from a workflow. It is the building block for pulling enriched product data and category trees from Akeneo into Shopware on a schedule, and for pushing Shopware products back into Akeneo when they change.

Pick an **Entity Type** (Product, Attribute, Family or Category), then an **Operation**: List records, Get record, Create or update record, or Delete record. Delete is only offered for products and families. Products are addressed by their Akeneo UUID in **Entity Identifier**; attributes, families and categories by their Akeneo code. List operations accept an optional **Search** payload in Akeneo's filter format and are paged with **Page Size**, **Offset** and **Maximum Pages**. When listing products, **Locale** (comma-separated codes such as `en_US,de_DE`) and **Channel Scope** (a channel code such as `ecommerce`) limit the localizable and scopable attribute values Akeneo returns, which keeps large product reads small.

:::info
**Node type:** `action.akeneo-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **akeneo** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Akeneo, create a connection for Nexus in your PIM's connection settings. Akeneo generates a client ID and secret and a username and password for it. Give the connection's user read and write permission on products, attributes, families and categories.
2. In Nexus, open **Integrations** and select **Akeneo**, then enter:
   - **PIM URL**, for example `https://your-instance.akeneo.com` (must use `https://`)
   - **Client ID** and **Client secret** of the connection
   - **Username** and **Password** of the connection
3. Use **Test connection** to verify the credentials before saving.

Nexus stores these values and exchanges them for an access token whenever the integration is used; you never paste a token yourself.

## Operations

- **List records** `list`
- **Get record** `get`
- **Create or update record** `upsert`
- **Delete record** `delete` (only when **Entity Type** is one of **Product**, **Family**)

## Node parameters

Configure this node with these parameters.

### Entity Type

Akeneo entity to operate on

- Type: Dropdown
- Required

Options:

- **Product** `product`
- **Attribute** `attribute`
- **Family** `family`
- **Category** `category`

### Operation

Akeneo operation to execute

- Type: Dropdown
- Required
- Default: `list`

Options:

- **List records** `list`
- **Get record** `get`
- **Create or update record** `upsert`
- **Delete record** `delete` (only when **Entity Type** is one of **Product**, **Family**)

### Entity Identifier

Product operations expect an Akeneo UUID; attribute, family, and category operations expect the Akeneo code

- Type: Text
- Optional
- Shown when **Operation** is one of **Get record**, **Create or update record**, **Delete record**

### Search

Optional Akeneo search payload for list operations

See the [Akeneo search format guide](https://api.akeneo.com/documentation/filter.html).

- Type: JSON
- Optional
- Shown when **Operation** is **List records**

### Locale

Optional Akeneo locale codes to return localizable product values for, comma-separated (for example en_US,de_DE)

- Type: Text
- Optional
- Shown when **Operation** is **List records** and **Entity Type** is **Product**

### Channel Scope

Optional Akeneo channel code to return scopable product values for (for example ecommerce)

- Type: Text
- Optional
- Shown when **Operation** is **List records** and **Entity Type** is **Product**

### Page Size

Number of Akeneo records to request per page (maximum 100)

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **List records**

### Offset

Starting offset for list pagination

- Type: Number
- Optional
- Default: `0`
- Shown when **Operation** is **List records**

### Maximum Pages

Safety cap for Akeneo pagination

- Type: Number
- Optional
- Default: `10`
- Shown when **Operation** is **List records**

### Request Body

JSON body for Akeneo upsert operations

- Type: JSON
- Optional
- Shown when **Operation** is **Create or update record**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input payload for Akeneo operations |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Akeneo API response |

**List records** returns the records fetched across the requested pages under `items`; **Get record** returns the single product, attribute, family or category as Akeneo returns it. **Create or update record** and **Delete record** return no body on success.

## Examples

### List products updated since a date

Connect a **Schedule Trigger** and configure:

| Parameter | Value |
| --- | --- |
| Entity Type | Product |
| Operation | List records |
| Search | `{"updated": [{"operator": ">", "value": "2026-09-01 00:00:00"}]}` |
| Locale | `en_US,de_DE` |
| Channel Scope | `ecommerce` |
| Page Size | `100` |
| Maximum Pages | `10` |

Pass the result to a **Field Mapping** node to reshape the products into Shopware payloads.

### Create or update a category

| Parameter | Value |
| --- | --- |
| Entity Type | Category |
| Operation | Create or update record |
| Entity Identifier | `shopware_sale` |
| Request Body | `{"code": "shopware_sale", "parent": "master", "labels": {"en_US": "Sale", "de_DE": "Angebote"}}` |

## Related resources

- [Akeneo REST API reference](https://api.akeneo.com/api-reference.html)
- [Akeneo search and filter format](https://api.akeneo.com/documentation/filter.html)
- [Akeneo authentication](https://api.akeneo.com/documentation/authentication.html)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Every Akeneo call fails after the connection changed in Akeneo**: the client ID, secret, username or password no longer match the Akeneo connection, for example after its secret was regenerated or the connection was deleted. Depending on how Akeneo answers the sign-in, the step shows **Authentication failed for the connected service** or **The connected service rejected the request**. Re-enter the values under **Integrations** and use **Test connection**.
- **Permission denied by the connected service**: the connection's user has no permission for that entity type or operation in Akeneo, typically on a write. Adjust the connection's permissions in Akeneo.
- **The requested resource was not found**: for products, **Entity Identifier** must be the product's Akeneo UUID, not its SKU or identifier attribute. For attributes, families and categories use the code.
- **The connected service rejected the request**: Akeneo refused the **Request Body** of **Create or update record**, for example a missing `code`, an unknown attribute, or a locale or channel that does not exist. Compare the body with the API reference for that entity.
- **The node configuration is invalid**: a required value is missing, for example an empty **Entity Identifier** or **Request Body**, or an expression in **Request Body**, **Locale** or **Channel Scope** does not resolve. For unresolved expressions the details name the token, so you can compare it with the fields of the node's input; the step fails rather than reading every locale or channel.
- **The connected service is rate limiting requests**: Akeneo throttled the requests. Nexus waits for the interval Akeneo asks for and retries, except for **Delete record**, which runs once. Reduce **Page Size** or run fewer workflows at the same time if it persists.
- **Delete is not offered**: only products and families can be deleted from this node. Delete attributes and categories in Akeneo.
