---
nav:
  title: "Klaviyo API"
  position: 11
---

# Klaviyo API

Use the Klaviyo API node to push Shopware customers, orders and products into Klaviyo and to read Klaviyo data back into a workflow. Typical uses are importing a customer as a Klaviyo profile, recording an order as a Placed Order event that drives flows, syncing products and categories as catalog items, and looking up profiles or list membership.

Pick an **Operation**. **Import or upsert profile**, **Create event**, **Create subscription bulk job** and **Add profiles to list** are purpose-built writes that take a JSON:API **Body** or a **Body template**. **List resources**, **Get resource**, **Create resource**, **Update resource** and **Delete resource** work on a **Resource** such as profiles, events, catalog items, catalog categories, lists or a **Custom resource** name for any other Klaviyo endpoint. List reads support a Klaviyo **Filter**, **Sort**, **Includes** and cursor pagination.

:::info
**Node type:** `action.klaviyo-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **klaviyo** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Klaviyo, create a private API key with read and write access to profiles, events, catalogs, lists and subscriptions.
2. In Nexus, open **Integrations** and select **Klaviyo**.
3. Enter the **Private API key** (it starts with `pk_`) and the **API revision** as a date in `YYYY-MM-DD` format, for example `2026-04-15`. Every request is sent with this revision.
4. Use **Test connection**, then save.

## Operations

- **List resources** `list`
- **Get resource** `get`
- **Create resource** `create`
- **Update resource** `update`
- **Delete resource** `delete`
- **Import or upsert profile** `profile_import`
- **Create event** `create_event`
- **Create subscription bulk job** `create_subscription_job`
- **Add profiles to list** `add_profiles_to_list`

## Node parameters

Configure this node with these parameters.

### Operation

Klaviyo operation to execute

- Type: Dropdown
- Required
- Default: `list`

Options:

- **List resources** `list`
- **Get resource** `get`
- **Create resource** `create`
- **Update resource** `update`
- **Delete resource** `delete`
- **Import or upsert profile** `profile_import`
- **Create event** `create_event`
- **Create subscription bulk job** `create_subscription_job`
- **Add profiles to list** `add_profiles_to_list`

### Resource

Klaviyo JSON:API resource for generic reads and writes. Use Custom resource for newer endpoints.

- Type: Dropdown
- Optional
- Default: `profiles`
- Shown when **Operation** is one of **List resources**, **Get resource**, **Create resource**, **Update resource**, **Delete resource**

Options:

- **Profiles** `profiles`
- **Events** `events`
- **Catalog items** `catalog-items`
- **Catalog categories** `catalog-categories`
- **Lists** `lists`
- **Subscription bulk jobs** `profile-subscription-bulk-create-jobs`
- **Custom resource** `custom`

### Custom resource

Exact Klaviyo resource name when Resource is Custom resource, such as coupons or catalog-variants.

- Type: Text
- Optional
- Placeholder: `catalog-variants`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Resource** is **Custom resource**

### Resource ID

Klaviyo resource ID for get, update, or delete operations

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Get resource**, **Update resource**, **Delete resource**

### Resource ID alias

Alternative resource ID field accepted by the executor for generated presets.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Get resource**, **Update resource**, **Delete resource**

### Body

JSON:API request body for create, update, profile import, events, subscriptions, and list relationship writes.

- Type: JSON
- Optional
- Shown when **Operation** is one of **Create resource**, **Update resource**, **Import or upsert profile**, **Create event**, **Create subscription bulk job**, **Add profiles to list**

### Body template

Template resolving to a JSON:API request body. Use @ mentions or <code v-pre>{{path.to.value}}</code> expressions from previous nodes.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create resource**, **Update resource**, **Import or upsert profile**, **Create event**, **Create subscription bulk job**, **Add profiles to list**

### List ID

Klaviyo list ID for list relationship or subscription operations

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create subscription bulk job**, **Add profiles to list**

### Filter

Klaviyo filter expression for list operations, such as equals(email,'buyer@example.com').

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List resources**

### Includes

Related resources to include in reads, for example ["lists", "segments"].

- Type: JSON
- Optional
- Shown when **Operation** is one of **List resources**, **Get resource**

### Sort

Klaviyo sort expression for list operations

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List resources**

### Additional fields

Klaviyo additional-fields map keyed by resource type, for example {"profile":["subscriptions"]}.

- Type: JSON
- Optional
- Shown when **Operation** is one of **List resources**, **Get resource**

### Query parameters

Additional Klaviyo query parameters for filters, includes, fields, or cursor overrides.

- Type: Key-value list
- Optional
- Shown when **Operation** is one of **List resources**, **Get resource**

### Page size

Number of Klaviyo resources to request per page

- Type: Number
- Optional
- Default: `50`
- Shown when **Operation** is **List resources**

### Limit

Maximum number of Klaviyo resources to return

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **List resources**

### Maximum pages

Safety cap for Klaviyo cursor pagination

- Type: Number
- Optional
- Default: `10`
- Shown when **Operation** is **List resources**

### Use workflow cursor

Resume list pagination from the workflow cursor and emit the next cursor for incremental runs.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **List resources**

### Cursor

Explicit Klaviyo page cursor for list operations

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **List resources**

### Metric name

Klaviyo metric name stored by event presets, such as Placed Order or Ordered Product.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create event**

### Unique ID

Stable Klaviyo event unique_id used by presets for idempotent event writes

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create event**

### External ID

Shopware or catalog external ID used by presets for idempotency and diagnostics

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create resource**, **Update resource**, **Import or upsert profile**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data used for Klaviyo request templates and JSON:API payloads |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Klaviyo API response, pagination cursor, and metadata |

The output object contains Klaviyo's JSON:API response: a `data` object for single-record operations or a `data` array for **List resources**, plus related resources when **Includes** is set. List reads also return the next page cursor; with **Use workflow cursor** enabled, the next run resumes from it.

## Examples

### Import a Shopware customer profile

Upsert a Klaviyo profile using a JSON:API profile-import payload from a mapping node.

```json
{
  "operation": "profile_import",
  "bodyJson": "{{profileImportPayload}}"
}
```

### Read marketing feedback

List profiles by email and include list or segment feedback for reverse enrichment.

```json
{
  "operation": "list",
  "resource": "profiles",
  "filter": "equals(email,'buyer@example.com')",
  "include": [
    "lists",
    "segments"
  ],
  "pageSize": 50,
  "maxPages": 2
}
```

### Record a Shopware order as a Placed Order event

Connect a **Shopware Event Trigger** listening for **Order placed** to this node and configure:

| Parameter | Value |
| --- | --- |
| Operation | Create event |
| Metric name | `Placed Order` |
| Unique ID | <code v-pre>{{data.orderNumber}}</code> |
| Body template | <code v-pre>{"data": {"type": "event", "attributes": {"metric": {"data": {"type": "metric", "attributes": {"name": "Placed Order"}}}, "unique_id": "{{data.orderNumber}}", "time": "{{data.orderDateTime}}", "value": {{data.amountTotal}}, "value_currency": "{{data.currency.isoCode}}", "properties": {"order_number": "{{data.orderNumber}}", "shopware_order_id": "{{data.id}}"}, "profile": {"data": {"type": "profile", "attributes": {"email": "{{data.orderCustomer.email}}", "external_id": "{{data.orderCustomer.customerNumber}}"}}}}}}</code> |

Using the order number as **Unique ID** lets Klaviyo ignore a second delivery of the same order.

## Related resources

- [Klaviyo API reference](https://developers.klaviyo.com/en/reference/api_overview)
- [Klaviyo: Create or update a profile](https://developers.klaviyo.com/en/reference/create_or_update_profile)
- [Klaviyo: Create an event](https://developers.klaviyo.com/en/reference/create_event)
- [Klaviyo: Filtering](https://developers.klaviyo.com/en/docs/filtering_)
- [Klaviyo: API versioning and revisions](https://developers.klaviyo.com/en/docs/api_versioning_and_deprecation_policy)
- [Klaviyo: Rate limits and error handling](https://developers.klaviyo.com/en/docs/rate_limits_and_error_handling)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Authentication failed for the connected service**: the private API key is wrong or was revoked in Klaviyo. Create a new key and update the credential under **Integrations**.
- **Permission denied by the connected service**: the key works but lacks the scope for the operation, for example write access to profiles, catalogs, lists or subscriptions. Edit the key's scopes in Klaviyo, or create a key with the access the workflow needs.
- **The connected service rejected the request**: Klaviyo did not accept the request. Common reasons are a **Filter** that does not follow Klaviyo's filter syntax (string values in single quotes and exact field names, for example `equals(email,'buyer@example.com')`), an **API revision** on the credential that is not a valid Klaviyo revision date in `YYYY-MM-DD` format, a **Body** or **Body template** that is not a valid JSON:API document for the endpoint, or **Create resource** on profiles for an email address that already has a profile (use **Import or upsert profile** instead).
- **The node configuration is invalid**: the operation needs a **Body** or **Body template** and neither is set, or an expression in **Body** points at a field the node's input does not have. In the second case the details list the expressions that could not be resolved. Expressions in **Body template** that cannot be resolved are left empty instead, so check the rendered request when a value is missing in Klaviyo.
- **The requested resource was not found**: the **Resource ID** or **List ID** does not exist in this Klaviyo account, or the **Custom resource** name is not a Klaviyo endpoint.
- **The connected service is rate limiting requests**: a large first sync is the usual cause, because **Import or upsert profile** sends one request per record. Reads and the purpose-built writes (**Import or upsert profile**, **Create event**, **Create subscription bulk job**, **Add profiles to list**) wait and retry automatically, so you only see this message when the retries run out; expect a long first run for a large customer base. **Create resource**, **Update resource** and **Delete resource** run once and fail on the first refusal.
- **The same order appears twice in Klaviyo**: **Create event** is retried after a timeout or rate limit, and Klaviyo counts each accepted event. Set **Unique ID** to a stable value such as the order number so Klaviyo ignores the repeat.
