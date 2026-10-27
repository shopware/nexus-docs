---
nav:
  title: "If"
  position: 1
---

# If

Use the If node to send data down one of two paths depending on a condition. Typical Shopware cases are only notifying a team about orders above a certain amount, treating B2B customers differently from B2C, or skipping products that are inactive.

Add one or more **Conditions**, each comparing a value (usually an expression such as <code v-pre>{{data.amountTotal}}</code>) with an operator to a second value. Choose whether all conditions must match (**AND**) or any of them (**OR**) under **Combine Conditions**. The incoming data is passed unchanged to the **True** or **False** output port.

:::info
**Node type:** `condition.if` · **Category:** Condition
:::

## Node parameters

Configure this node with these parameters.

### Conditions

Array of conditions to evaluate

- Type: Condition builder
- Required
- Default: `{"condition": [{"value1": "", "operation": "==", "value2": ""}]}`

Options:

- **>** `>`
- **>=** `>=`
- **<** `<`
- **<=** `<=`
- **==** `==`
- **!=** `!=`
- **Contains** `contains`
- **Does not contain** `notContains`
- **Starts with** `startsWith`
- **Ends with** `endsWith`
- **Is empty** `isEmpty`
- **Is not empty** `isNotEmpty`
- **Is not set** `isNil`
- **Is set** `isNotNil`

### Combine Conditions

- Type: Dropdown
- Required
- Default: `AND`

Options:

- **AND** `AND`
- **OR** `OR`

### Options

Additional options for condition evaluation

- Type: Condition builder
- Optional
- Default: `{"ignoreCase": false, "looseTypeValidation": false}`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data to evaluate |

### Output

| Port | Type | Description |
| --- | --- | --- |
| True | `any` | Output when condition is true |
| False | `any` | Output when condition is false |

The node does not change the data. Whatever came in is forwarded to the **True** port when the combined condition holds and to the **False** port otherwise.

## Examples

### Alert only on large orders

Connect a **Shopware Event Trigger** listening for **Order placed** to this node:

| Parameter | Value |
| --- | --- |
| Conditions | <code v-pre>{{data.amountTotal}}</code> — **>=** — `500` |
| Combine Conditions | AND |

Connect the **True** port to a **Send Slack Message** node. Leave **False** unconnected if small orders need no action.

### Route B2B customers

| Parameter | Value |
| --- | --- |
| Conditions | <code v-pre>{{data.orderCustomer.company}}</code> — **Is not empty** |
| Combine Conditions | AND |

Send the **True** branch to your ERP and the **False** branch to a CRM.

## Common issues

- **A numeric comparison behaves like text**: the compared values must be numbers for `>`, `>=`, `<` and `<=`. If a value arrives as a string, enable the loose type validation option so it is converted before comparison.
- **Contains or Starts with does not match**: string operators are case-sensitive by default. Enable the ignore case option or normalise the value upstream.
- **Both ports fire or neither fires**: an If node only has the **True** and **False** ports. Check that each outgoing connection starts from the intended port.
- **The condition is always false for a missing field**: use **Is set** or **Is not set** to test for the presence of a field before comparing its value.
