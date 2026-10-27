---
nav:
  title: "Shopware API Call"
  position: 29
---

# Shopware API Call

Use the Shopware API Call node to read and write data in your Shopware shop from a workflow. It covers products, orders, customers, newsletter recipients, categories, media and tags, and through **Other entity…** any other Shopware entity by its technical name, so you can look up an order after an event, pull the full catalogue on a schedule, write ERP data back into Shopware as products or customers, or load price lists into a custom entity.

Choose a **Connection Method**: **Connected shop** uses a shop linked to your Nexus workspace, **API credentials** uses a saved Shopware credential. Then pick an **Entity Type** and an **Operation**: **Get all**, **Get one**, **Search**, **Create or update** or **Update existing only**. For **Other entity…**, enter the **Entity name** as Shopware spells it, for example `product_price` or `custom_entity_price_list`. Entities that control access, run code or hold secrets (such as `user`, `integration`, `app`, `flow`, `system_config` or `webhook`) are refused. A connected shop can only write the entities the Nexus app is allowed to write, so custom entities of other apps need **API credentials** with an integration whose role grants that entity.

**Get one** takes the record ID from the incoming data (`id` or `entityId`). For **Get all** you can enable **Sync only new and updated data** so later runs only fetch changes; for **Get all** and **Search** you can restrict **Response Fields** to the fields the workflow needs. **Search** adds **Search Criteria** and a **Limit**. Write operations send the incoming data, usually the output of a **Field Mapping** node. **Create or update** matches existing products by `productNumber`, orders by `orderNumber` and newsletter recipients by `email`; **Update existing only** skips records that do not exist yet instead of creating them.

Two settings shape large product loads. **Index in the background (bulk loads)** lets Shopware write the records first and update its search and listing indexes through its message queue, which keeps big writes fast; storefront listings show the change once the shop's queue workers have run. **Visibilities and categories on existing products** decides what a product write does to products that already exist: **Set only when the product is created** (the default) leaves their sales channel visibilities and categories alone, **Add and update, never remove** adds and updates them, and **Replace within the managed sales channels** also removes visibilities in the selected **Managed sales channels** that the incoming product no longer lists. In the last two modes, categories are assigned by ID and are never created or renamed.

:::info
**Node type:** `action.shopware-api-call` · **Category:** Action
:::

## Credentials

:::info
A **shopware** credential is optional for this node. The steps below explain when you need one.
:::

With **Connected shop**, no credential is needed: link the shop from your Shopware admin (**Settings → System → Nexus** and choose **Link this shop**) and select it under **Shopware Shop**.

With **API credentials**:

1. In your Shopware admin, open **Settings → System → Integrations** and create an integration with the permissions the workflow needs. Copy the access key ID and secret access key.
2. In Nexus, open **Integrations** and select **Shopware**. Enter the **Shop URL**, the access key ID as **Client ID** and the secret as **Client secret**.
3. Use **Test connection**, then save the credential and select it in the node's **Credentials** tab.

## Operations

- **Get all** `getAll`
- **Get one** `getOne`
- **Search** `search`
- **Create or update** `createOrUpdate`
- **Update existing only** `update`

## Node parameters

Configure this node with these parameters.

### Connection Method

How to connect to the Shopware shop

- Type: Dropdown
- Required
- Default: `integration`

Options:

- **Connected shop** `integration`
- **API credentials** `credentials`

### Shopware Shop

Select the Shopware shop to call the API on

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Connection Method** is **Connected shop**

### Entity Type

Shopware entity to operate on

- Type: Dropdown
- Required

Options:

- **Product** `product`
- **Order** `order`
- **Customer** `customer`
- **Newsletter recipient** `newsletter-recipient`
- **Category** `category`
- **Media** `media`
- **Tag** `tag`
- **Other entity…** `__custom__`

### Entity name

Technical Shopware entity name, e.g. custom_entity_price_list or product_price

- Type: Text
- Required
- Placeholder: `custom_entity_…`
- Shown when **Entity Type** is **Other entity…**

### Operation

Action to perform on the selected entity

- Type: Dropdown
- Required

Options:

- **Get all** `getAll`
- **Get one** `getOne`
- **Search** `search`
- **Create or update** `createOrUpdate`
- **Update existing only** `update`

### Sync only new and updated data

After the first run, only new or updated data will be synced. Deleted or unchanged data will not be updated automatically.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Get all**

### Response Fields

Fetch only these fields for each record. Leave empty to fetch complete records. Selecting just the fields the workflow uses keeps large reads inside the per-step payload limit.

- Type: Multi-select
- Optional
- Options are loaded from your connected account
- Shown when **Operation** is one of **Get all**, **Search**

### Search Criteria

Criteria used to search Shopware entities

- Type: Condition builder
- Required
- Default: `{"condition": [{"value1": "", "operation": "equals", "value2": ""}]}`
- Shown when **Operation** is **Search**

Options:

- **Equals** `equals`
- **Equals any** `equalsAny`
- **Contains** `contains`
- **Prefix** `prefix`
- **Suffix** `suffix`

### Limit

Number of Shopware records to fetch per request; must be between 100 and 10000 in steps of 100

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **Search**

### Index in the background (bulk loads)

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **create**, **Create or update**, **Update existing only**

### Visibilities and categories on existing products

- Type: Dropdown
- Optional
- Default: `createOnly`
- Shown when **Entity Type** is **Product** and **Operation** is one of **Create or update**, **Update existing only**

Options:

- **Set only when the product is created** `createOnly`
- **Add and update, never remove** `merge`
- **Replace within the managed sales channels** `replace`

### Managed sales channels

- Type: Multi-select
- Required
- Options are loaded from your connected account
- Shown when **Entity Type** is **Product** and **Operation** is one of **Create or update**, **Update existing only** and **Visibilities and categories on existing products** is **Replace within the managed sales channels**

### Timeout (ms)

- Type: Number
- Optional
- Default: `10000`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | API response from Shopware |

**Get all** and **Search** return a list of the matching records, limited to the selected **Response Fields** if you set any; **Get one** returns the single record. **Create or update** and **Update existing only** return the records that were written, as they were sent to Shopware. The node also passes the shop's address on as <code v-pre>{{shopUrl}}</code>, so a node directly after it can build storefront links.

## Examples

### Reload the full order after a payment event

Connect a **Shopware Event Trigger** listening for **Payment status changed to paid** (ID `state_enter.order_transaction.state.paid`) to this node. The trigger output carries the order's `id`, which **Get one** uses:

| Parameter | Value |
| --- | --- |
| Connection Method | Connected shop |
| Shopware Shop | your production shop |
| Entity Type | Order |
| Operation | Get one |

### Find customers by email domain

| Parameter | Value |
| --- | --- |
| Connection Method | Connected shop |
| Shopware Shop | your production shop |
| Entity Type | Customer |
| Operation | Search |
| Search Criteria | `email` — **Suffix** — `@example.com` |
| Response Fields | `id`, `email`, `customerNumber` |
| Limit | `100` |

### Write ERP items as products

Run a **Schedule Trigger**, read items from your ERP, map them with a **Field Mapping** preset, then configure:

| Parameter | Value |
| --- | --- |
| Connection Method | Connected shop |
| Shopware Shop | your production shop |
| Entity Type | Product |
| Operation | Create or update |
| Index in the background (bulk loads) | enabled |
| Visibilities and categories on existing products | Add and update, never remove |

## Related resources

- [Shopware: Admin API guide](https://developer.shopware.com/docs/guides/integrations-api/)
- [Shopware: search criteria](https://developer.shopware.com/docs/guides/integrations-api/general-concepts/search-criteria.html)
- [Shopware: creating an integration](https://docs.shopware.com/en/shopware-6-en/settings/system/integrationen)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node output is too large. Reduce max records or selected fields.**: a **Get all** or **Search** returned more data than one step may carry; the details show the limit. Select only the **Response Fields** the workflow uses, lower the **Limit**, or narrow the **Search Criteria**. Writes echo the records they wrote, so very large write batches can hit the same limit.
- **The node configuration is invalid**: the step was refused before or by Shopware because of a setting. Typical causes are an **Entity name** that is not available to workflows, a read of an entity the shop does not define (the details name the entity), a product payload whose visibilities name a sales channel outside **Managed sales channels** or a category without an ID (the details name the record and field), **Update existing only** on an entity that has no unique key and no `id` in the data, or a shop that has been disconnected from Nexus since the workflow was saved.
- **Permission denied by the connected service**: the Shopware integration may not perform the operation. The details name the missing privilege, for example `product_visibility:delete`, which **Replace within the managed sales channels** needs. With **API credentials**, grant it to the integration's role in the Shopware admin; with **Connected shop**, use **API credentials** for entities the Nexus app cannot write. If Shopware refuses the access key itself, typically because the integration was deleted or its secret regenerated, Nexus shows **Authentication failed for the connected service**; update the credential under **Integrations**, or re-link a connected shop.
- **The connected service rejected the request**: Shopware refused one or more records of a write. The details name the first rejected record and field, for example `record 3 rejected: field stock`. Fix the mapping for that field and run the workflow again. A write to an entity the shop does not define also ends here, with the entity named in the details.
- **The Shopware shop is rate limiting requests. Check the request limits set by the shop or its hosting provider.**: the shop or the hosting in front of it throttled Nexus. Reads are retried; a write is retried up to three times only when every record carries an `id`, otherwise it runs once. Lower the batch size or space out runs.
- **Update existing only writes fewer records than it received**: records whose `productNumber`, `orderNumber` or `email` (or `id`) does not match an existing record are skipped, never created, and the step still succeeds. Use **Create or update** when records may be new.
- **Sync only new and updated data returns the same records again**: the sync position is saved only after the whole workflow run succeeds, so a run that fails in a later node reads the same changes next time. Deleted records are never reported; handle deletions separately.
