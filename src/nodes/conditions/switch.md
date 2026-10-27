---
nav:
  title: "Switch"
  position: 2
---

# Switch

Use the Switch node to route data to one of several branches based on a single value. It replaces a chain of If nodes when one field decides the path, for example the order's payment method, the delivery country, the sales channel or the customer group.

Enter the **Switch field** as an expression such as <code v-pre>{{data.transactions.0.paymentMethod.name}}</code>, choose a **Match mode** (**Equals**, **Contains** or **Regex**) and add **Cases**. Each case has a value and its own output port; connect each port to the branch that should handle it.

:::info
**Node type:** `condition.switch` · **Category:** Condition
:::

## Node parameters

Configure this node with these parameters.

### Switch field

Field path or expression to evaluate

- Type: Text
- Required

### Match mode

How to match the value

- Type: Dropdown
- Required
- Default: `equals`

Options:

- **Equals** `equals`
- **Contains** `contains`
- **Regex** `regex`

### Cases

List of value → output port mappings

- Type: Switch cases
- Required
- Default: `{"cases": [{"value": ""}]}`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data to evaluate |

### Output

None.

The node does not change the data. The incoming data is forwarded unchanged to the output port of the matching case.

## Examples

### Route orders by delivery country

Connect a **Shopware Event Trigger** listening for **Order placed** to this node:

| Parameter | Value |
| --- | --- |
| Switch field | <code v-pre>{{data.deliveries.0.shippingOrderAddress.country.iso}}</code> |
| Match mode | Equals |
| Cases | `DE`, `AT`, `CH` |

Connect the `DE` port to the domestic carrier's API, and `AT` and `CH` to the export flow.

### Group payment methods by name

| Parameter | Value |
| --- | --- |
| Switch field | <code v-pre>{{data.transactions.0.paymentMethod.name}}</code> |
| Match mode | Contains |
| Cases | `Invoice`, `PayPal` |

## Common issues

- **A case never matches**: with **Equals** the value must match exactly, including case and whitespace. Check the sample data of the previous node and use **Contains** or **Regex** for looser matching.
- **A switch node has multiple branches for the same output port**: each case port may have only one outgoing connection. Merge the following steps into one branch.
- **The Switch field resolves to nothing**: the expression path is wrong or the field is nested in an array. Use an index such as `.0.` for the first element.
- **Regex does not behave as expected**: the pattern is applied to the whole value as a string. Test it against a real sample before publishing.
