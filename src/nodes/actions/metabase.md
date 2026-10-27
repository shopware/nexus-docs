---
nav:
  title: "Metabase"
  position: 15
---

# Metabase

Use the Metabase node to run a saved Metabase question and use its result rows in a workflow. It is read-only and turns your analytics into workflow inputs: post a daily revenue digest to Slack, alert on a low-stock or fraud query, or look up a customer's lifetime value from your warehouse when an order arrives.

The node has one operation. Pick the **Saved question** from the dropdown, which lists the questions the API key can see, and optionally supply **Parameters** for a question with variables, in Metabase's parameter JSON format. Leave Parameters empty to run the question with its defaults.

:::info
**Node type:** `action.metabase` · **Category:** Action
:::

## Credentials

:::info
This node uses a **metabase** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Metabase, create an API key under **Settings → Admin → API Keys** and assign it to a group that can view the questions you want to run.
2. In Nexus, open **Integrations** and select **Metabase**, then enter:
   - **Metabase URL**, for example `https://metabase.example.com`
   - **API key**
3. Use **Test connection** to verify the key before saving.

## Node parameters

Configure this node with these parameters.

### Saved question

Metabase saved question (card) to run.

- Type: Dropdown
- Required
- Options are loaded from your connected account

### Parameters

Optional Metabase parameters array for parameterized questions. Omit to run with the question's defaults.

- Type: JSON
- Optional
- Placeholder: `[{"type": "number/=", "target": ["variable", ["template-tag", "min_qty"]], "value": 7}]`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for the Metabase question |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Metabase question result rows |

The output is an array of rows, one object per row keyed by the question's column names, so a question returning `order_number` and `total` yields `[{"order_number": "10001", "total": 129.9}, …]`. Metabase names aggregate columns itself (`sum`, `count`, `avg`); read the exact names from the node's output preview. An empty result is a valid output, not an error.

## Examples

### Look up a customer's lifetime value when an order is placed

Create a native question in Metabase with a `customer_id` variable, then connect a **Shopware Event Trigger** listening for **Order placed** and configure:

| Parameter | Value |
| --- | --- |
| Saved question | Customer lifetime value |
| Parameters | <code v-pre>[{"type": "text", "target": ["variable", ["template-tag", "customer_id"]], "value": "{{data.orderCustomer.customerId}}"}]</code> |

A parameter value that is exactly one expression keeps the type of the resolved data, so numbers stay numbers. An expression that cannot be resolved fails the step instead of sending an empty value.

### Post a daily summary to Slack

Connect a **Schedule Trigger** to this node with a single-row summary question, then a **Send Slack Message** node with <code v-pre>Revenue yesterday: {{0.total}} EUR from {{0.orders}} orders</code>.

## Related resources

- [Metabase API documentation](https://www.metabase.com/docs/latest/api)
- [Metabase API keys](https://www.metabase.com/docs/latest/people-and-groups/api-keys)
- [Metabase SQL variables](https://www.metabase.com/docs/latest/questions/native-editor/sql-parameters)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: most often the saved question itself failed, because its SQL or the warehouse returned an error. Open the question in Metabase to see the full error; the run is not retried, because Metabase does not distinguish broken SQL from a temporary warehouse problem. The same message appears when the result is larger than 2 MB (narrow the question to fewer rows or columns), when **Parameters** is not a JSON array of parameter objects, when a parameter's `template-tag` name does not match a variable of the question, or when an expression in **Parameters** cannot be resolved.
- **Authentication failed for the connected service**: Metabase rejected the API key, usually because it was deleted or regenerated. Create a new key and update the credential under **Integrations**.
- **The requested resource was not found**: the saved question no longer exists, or the **Metabase URL** points to a different instance. Pick the question again from **Saved question**.
- **Permission denied by the connected service**: the API key's group cannot view the question or query its database. Grant the group access in Metabase's permission settings.
- **A Slack message or condition shows a blank value**: the output is an array of rows. Address a row by index, for example <code v-pre>{{0.total}}</code> for the first row; a bare <code v-pre>{{total}}</code> only resolves in a message when the question returns exactly one row, and never in a condition.
- **The output preview shows no fields**: every variable of the question needs a default value in Metabase for the preview to run.
- **A long-running question fails**: the node waits up to five minutes for the warehouse to answer. Speed up the question or pre-aggregate the data in Metabase. Saved questions must be read-only SQL; questions that modify data are not supported.
