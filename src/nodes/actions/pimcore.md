---
nav:
  title: "Pimcore API"
  position: 21
---

# Pimcore API

Use the Pimcore API node to read and write Pimcore data objects and assets through a Pimcore Data Hub GraphQL configuration. It lets you sync Pimcore product objects, category trees and DAM asset metadata into Shopware, poll for objects changed since the last run, and create objects in Pimcore from Shopware products.

The node is driven by your Data Hub schema rather than a fixed catalogue. After you select a Pimcore credential, **Entity Type** lists the classes and asset types the Data Hub configuration exposes, and **Operation** lists what the schema allows for that entity: list, get, create, update or delete. **Selected Fields** offers the fields of the chosen class. There is no create-or-update operation: choose create or update yourself, and give update or delete the numeric **Entity ID**, from the node config or from the incoming data.

:::info
**Node type:** `action.pimcore-api` · **Category:** Action
:::

:::warning
**Experimental**
This node is experimental and its behaviour may change.
:::

## Credentials

:::info
This node uses a **pimcore** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Pimcore, open **Datahub** and create or open the GraphQL configuration Nexus should use. The configuration name becomes part of the endpoint, `/pimcore-graphql-webservices/{configuration-name}`.
2. Under **Security Definition**, use **API key** authentication, create or copy the API key, and make sure introspection is not disabled. Nexus discovers entities, operations and fields from the schema, so the connection test fails without introspection.
3. Under **Schema Definition**, add every class you want to read to the Query Schema, and every class you want to create, update or delete to the Mutation Schema, ticking Create, Update or Delete as needed. Expose asset reading if you read DAM metadata.
4. Under **Permissions**, add workspace rows for the data object and asset folders Nexus may access and tick Read, Create, Update or Delete per path. Use the picker icon in the Path cell; a blank path row grants nothing. Without a workspace, Pimcore returns an empty schema or denies access.
5. In Nexus, open **Integrations** and select **Pimcore**, then enter:
   - **Pimcore URL**: the base URL only, for example `https://pimcore.example.com`, not the `/admin` path or the GraphQL endpoint
   - **Datahub configuration name**, for example `nexus`
   - **Datahub API key**
6. Run **Test connection**. The integration can only be saved after a successful test.

## Node parameters

Configure this node with these parameters.

### Entity Type

Pimcore Datahub class or entity exposed by the selected integration.

- Type: Dropdown
- Required
- Placeholder: `Select a Pimcore entity`
- Options are loaded from your connected account

### Operation

Pimcore Datahub operation to execute

- Type: Dropdown
- Required
- Default: `list`
- Options are loaded from your connected account

### Entity ID

Object or asset ID for get, update, or delete. Can be supplied by previous node data.

- Type: Text
- Optional
- Shown when **Operation** is one of **get**, **update**, **delete**

### Filter

Pimcore Datahub listing filter JSON, for example {"published":true}.

- Type: JSON
- Optional
- Shown when **Operation** is **list**

### Selected Fields

Fields to request from Pimcore. Keep this narrow for bulk sync.

- Type: Multi-select
- Optional
- Default: `["id"]`
- Options are loaded from your connected account
- Shown when **Operation** is one of **list**, **get**

### Max Records

Maximum records to read for list operations.

- Type: Number
- Optional
- Default: `50`
- Shown when **Operation** is **list**

### Include Children Depth

Depth for nested Pimcore object relations when supported by the Datahub schema.

- Type: Number
- Optional
- Default: `0`
- Shown when **Operation** is **list**

### Language

Optional Pimcore defaultLanguage argument for localised fields, such as en or de.

- Type: Text
- Optional
- Placeholder: `en`

### Use Cursor Watermark

For list operations, use workflow watermark metadata to poll records changed since the previous run.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **list**

### Root Records Only

For list operations, return only Pimcore records without a parent object.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **list**

### Use Fallback Language

Allow Pimcore localised fields to fall back when the requested language has no value.

- Type: Toggle
- Optional
- Default: `true`

### Confirm Destructive Delete

Must be enabled before running a destructive Pimcore delete mutation. Prefer unpublish or archive workflows when possible.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **delete**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input object for Pimcore create, update, or delete operations |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Pimcore Datahub operation result |

The output object contains the Data Hub response data and execution metadata. **list** returns the matching objects with the **Selected Fields**, up to **Max Records**; **get** returns one object. Create, update and delete return the result of the Data Hub mutation. When **Use Cursor Watermark** is enabled, a list only returns records changed since the previous run of the workflow.

## Examples

### Read Cars from Pimcore

Fetch Car objects with a narrow field projection.

```json
{
  "entityType": "Car",
  "operation": "list",
  "filter": {
    "objectType": "catalog"
  },
  "select": [
    "id",
    "classname",
    "name",
    "modificationDate"
  ],
  "maxRecords": 50
}
```

### Create a Pimcore Car

Create a Car object from a mapped workflow payload.

```json
{
  "entityType": "Car",
  "operation": "create",
  "language": "en"
}
```

### Poll categories changed since the last run

Connect a **Schedule Trigger** and configure:

| Parameter | Value |
| --- | --- |
| Entity Type | Category |
| Operation | list |
| Selected Fields | `id`, `name`, `fullpath`, `modificationDate` |
| Max Records | `200` |
| Use Cursor Watermark | enabled |
| Language | `en` |

Feed the output into a **Field Mapping** node to build Shopware category payloads.

## Related resources

- [Pimcore Data Hub GraphQL](https://github.com/pimcore/data-hub/blob/2.x/doc/10_GraphQL/README.md)
- [Pimcore Data Object Mutations](https://github.com/pimcore/data-hub/blob/2.x/doc/10_GraphQL/07_Mutation/20_DataObject_Mutations.md)
- [Pimcore Data Hub GraphQL documentation](https://docs.pimcore.com/platform/Datahub/GraphQL/)
- [Data Hub security settings](https://docs.pimcore.com/platform/Datahub/GraphQL/Configuration/Security_Settings/)
- [Data Hub endpoint URL](https://docs.pimcore.com/platform/Datahub/GraphQL/Configuration/Customize_Endpoint_URL/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Datahub endpoint not found**: the **Pimcore URL** or **Datahub configuration name** is wrong. Enter the base URL only and the exact configuration name.
- **Pimcore rejected the API key** or **Pimcore denied access**: the key belongs to a different configuration, or the workspace permissions do not cover the folders you access. Check the Security Definition and Permissions of the configuration.
- **Entity Type dropdown is empty or the GraphQL test request is rejected**: introspection is disabled for the configuration, or no workspace is selected. Enable introspection and add workspace rows.
- **Delete fails immediately**: **Confirm Destructive Delete** must be enabled on the node, and the configuration needs Delete on the object path.
- **Re-running a create workflow duplicates objects**: there is no upsert. Use update with the object's **Entity ID** for existing objects, or look the object up before creating.
- **A scheduled poll keeps returning objects your own writeback changed**: writing to Pimcore advances `modificationDate`, so the next poll sees the object as changed. Scope the two workflows to different object paths or compare an external reference before writing.
- **The connection test passes but a list returns nothing**: check the workspace paths, filter, class schema fields and **Root Records Only** setting for the entity.
