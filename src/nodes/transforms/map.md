---
nav:
  title: "Field Mapping"
  position: 1
---

# Field Mapping

Use the Field Mapping node to reshape data from one system into the structure another system expects. It sits between a read step and a write step: for example between a **Shopware Event Trigger** and an ERP node to turn a Shopware order into a sales order, or between an ERP read and a **Shopware API Call** to turn ERP items into Shopware products.

The node takes the incoming data and applies a list of **Field Mapping** rows, each copying a source field to a target field, optionally through a transform such as inverting a boolean, calculating a price with tax, or splitting a name. **Defaults** is a JSON object of fixed values merged into every mapped record, for example a currency, tax rate or sales channel ID. Instead of building rows by hand you can pick a **Mapping preset**: ready-made mappings for the common Shopware-to-connector and connector-to-Shopware flows (orders, customers, products, categories, stock) that you can then adjust. Some presets ask for required configuration values, such as the Shopware currency or a default customer group, before they can run.

:::info
**Node type:** `transform.map` · **Category:** Transform
:::

## Node parameters

Configure this node with these parameters.

### Field Mapping

Define field mapping rules between source and target

- Type: Field mapping
- Optional

### Defaults

Optional JSON object of static default values merged into every mapped record.

- Type: JSON
- Optional

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data to transform |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `any` | Transformed output data |

The output is the mapped data. When the input is a list of records, every record is mapped and the output is a list of the same length; when the input is a single object, the output is a single mapped object.

## Examples

### Turn a Shopware order into an ERP sales order

Connect a **Shopware Event Trigger** listening for **Order placed** to this node and choose the preset for your ERP, for example **Shopware Order to ERPNext Sales Order**. The preset fills the rows for you; a hand-built equivalent looks like this:

| Source | Target |
| --- | --- |
| `orderNumber` | `external_ref` |
| `orderDateTime` | `transaction_date` |
| `currency.isoCode` | `currency` |
| `lineItems` | `items` |

| Parameter | Value |
| --- | --- |
| Defaults | `{"company": "My Company GmbH", "delivery_date": "2026-10-01"}` |

Connect the output to the ERP node's create-or-update operation.

### Map ERP items to Shopware products

| Source | Target |
| --- | --- |
| `item_code` | `productNumber` |
| `item_name` | `name` |
| `standard_rate` | `price.0.net` |
| `disabled` | `active` (transform: Inverted) |

| Parameter | Value |
| --- | --- |
| Defaults | `{"taxId": "<UUID of the tax rate>", "price": [{"currencyId": "<UUID of the currency>"}]}` |

Follow with a **Shopware API Call** using **Entity Type** Product and **Operation** Create or update.

## Common issues

- **Missing required default values**: the chosen preset needs values such as the Shopware currency, tax rate or a default customer group. Fill them in under the required configuration section of the node.
- **Maps multiple rows to the same target**: two rows write the same target field. Remove or rename one of them.
- **A target field is empty after mapping**: the source path does not exist in the incoming data. Check the sample data of the previous node; nested and array fields need a full path such as `price.0.net`.
- **Custom rows disappeared**: choosing **Reset to preset** discards your changes and restores the preset's rows. Adjust rows after selecting the preset rather than re-selecting it.
- **Shopware rejects the mapped record**: Shopware needs UUIDs for references such as `taxId`, `currencyId` or `salesChannelId`. Provide them in **Defaults**; the node offers dropdowns for common IDs once the target shop credential is selected.
