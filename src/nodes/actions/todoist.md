---
nav:
  title: "Todoist API"
  position: 31
---

# Todoist API

Use the Todoist API node to create and manage Todoist tasks, projects, sections, comments, labels and reminders from a workflow. It is a simple way to hand work to a person: create a task when a Shopware order needs a manual check, when a product is missing data, or when a sync with another system fails, and close or reopen it later from another workflow.

Pick an **Entity**, then an **Operation**; the operations offered follow the entity, for example **Close**, **Reopen**, **Move** and **Quick add** exist only for tasks and **Archive**, **Unarchive** and **Get collaborators** only for projects. Write operations take a JSON **Payload** in the shape of the Todoist API; operations on an existing record take its **Resource ID**, and **Get many** for tasks accepts a Todoist filter query.

:::info
**Node type:** `action.todoist-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **todoist** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **Todoist**.
2. Choose **Connect** to authorise Nexus with your Todoist account through Todoist's OAuth flow, or enter a **Personal API token** manually. You find the token in Todoist under Settings → Integrations → Developer.
3. Use **Test connection** to verify the credential before saving.

Tasks and comments created through the node belong to the Todoist account that authorised the credential.

## Operations

- **Close** `close` (only when **Entity** is **Task**)
- **Create** `create`
- **Delete** `delete`
- **Get** `get` (only when **Entity** is not **Reminder**)
- **Get many** `get_many`
- **Move** `move` (only when **Entity** is **Task**)
- **Quick add** `quick_add` (only when **Entity** is **Task**)
- **Reopen** `reopen` (only when **Entity** is **Task**)
- **Update** `update`
- **Archive** `archive` (only when **Entity** is **Project**)
- **Get collaborators** `get_collaborators` (only when **Entity** is **Project**)
- **Unarchive** `unarchive` (only when **Entity** is **Project**)

## Node parameters

Configure this node with these parameters.

### Entity

Todoist resource collection for generic create, update, get, list, and filter operations

- Type: Dropdown
- Required
- Default: `task`

Options:

- **Task** `task`
- **Project** `project`
- **Section** `section`
- **Comment** `comment`
- **Label** `label`
- **Reminder** `reminder`

### Operation

Todoist operation to execute

- Type: Dropdown
- Required
- Default: `create`

Options:

- **Close** `close` (only when **Entity** is **Task**)
- **Create** `create`
- **Delete** `delete`
- **Get** `get` (only when **Entity** is not **Reminder**)
- **Get many** `get_many`
- **Move** `move` (only when **Entity** is **Task**)
- **Quick add** `quick_add` (only when **Entity** is **Task**)
- **Reopen** `reopen` (only when **Entity** is **Task**)
- **Update** `update`
- **Archive** `archive` (only when **Entity** is **Project**)
- **Get collaborators** `get_collaborators` (only when **Entity** is **Project**)
- **Unarchive** `unarchive` (only when **Entity** is **Project**)

### Resource ID

Opaque Todoist resource ID for get, update, delete, archive, or unarchive operations

- Type: Text
- Optional
- Placeholder: <code v-pre>{{id}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Close**, **Get**, **Update**, **Delete**, **Move**, **Reopen**, **Archive**, **Unarchive**, **Get collaborators**

### Task Filter Query

Todoist filter query for filtering tasks

- Type: Text
- Optional
- Placeholder: `today | overdue`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Task** and **Operation** is **Get many**

### Payload

Optional Todoist request payload; if omitted, the node uses incoming workflow data

Please read the payload from &lt;a target="_blank" href="https://developer.todoist.com/api/v1">Todoist apidoc&lt;/a>

- Type: JSON
- Optional
- Placeholder: `{"content":"Review the order"}`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create**, **Quick add**, **Update**

### Page size

Maximum resources per Todoist page

- Type: Number
- Optional
- Default: `50`
- Shown when **Operation** is **Get many**

### Max pages

Maximum pages to read before stopping

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **Get many**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input payload for the Todoist operation |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Todoist API response |

The output object contains Todoist's response as-is: the created or updated task, project, section, comment, label or reminder for write operations, and the matching records collected across the requested pages for **Get many**. **Close** and **Reopen** return an empty result on success, and **Delete** returns `deleted` and the `id` of the removed record.

## Examples

### Create a task

Create a task using a JSON payload.

```json
{
  "entity": "task",
  "operation": "create",
  "payload": {
    "content": "Review the order",
    "priority": 4
  }
}
```

### List all tasks

Leave the filter query empty to read all tasks.

```json
{
  "entity": "task",
  "operation": "get_many"
}
```

### Create a review task from a Shopware order

Connect a **Shopware Event Trigger** listening for **Order placed** to this node and configure:

| Parameter | Value |
| --- | --- |
| Entity | Task |
| Operation | Create |
| Payload | <code v-pre>{"content": "Review order {{data.orderNumber}}", "description": "Total {{data.amountTotal}} EUR. Check payment and address before fulfilment.", "due_string": "today", "priority": 4}</code> |

Add `"project_id"` to the payload to put the task in a specific project instead of the inbox.

### List overdue tasks

| Parameter | Value |
| --- | --- |
| Entity | Task |
| Operation | Get many |
| Task Filter Query | `overdue` |
| Page size | `50` |
| Max pages | `10` |

## Related resources

- [Todoist API v1](https://developer.todoist.com/api/v1/)
- [Todoist OAuth](https://developer.todoist.com/api/v1/#tag/Authorization)
- [Todoist: Find your API token](https://www.todoist.com/help/articles/find-your-api-token-Jpzx9IIlB)
- [Todoist: Introduction to filters](https://www.todoist.com/help/articles/introduction-to-filters-V8Xd2)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Authentication failed for the connected service**: Todoist rejected the token, usually because it was revoked or a new API token was issued, which invalidates the old one. Re-authorise the credential under **Integrations**.
- **Permission denied by the connected service**: the token is valid, but the Todoist account has no access to the project or record, for example a shared project it was removed from.
- **The requested resource was not found**: the **Resource ID** does not exist, was deleted, or belongs to another Todoist account.
- **The node configuration is invalid**: the operation is missing an input it needs. If **Resource ID** is empty, the node looks for the entity's ID field (for example `task_id`), `id` or `resourceId` in the payload; set **Resource ID** explicitly when the incoming data does not carry one. **Create** and **Update** also need a **Payload** or incoming data.
- **The connected service rejected the request**: Todoist refused the payload. The **Payload** must be valid JSON using the field names of the Todoist API, for example `content`, `description`, `due_string`, `priority` and `project_id`. Expressions inside string values are fine.
- **The connected service is rate limiting requests**: Todoist limits requests per user. Reads wait and try again; write operations are not retried and fail straight away. Spread bursts with a Delay node or aggregate records before writing.
- **Get many returns fewer records than expected**: results stop at **Max pages** × **Page size**. Raise either value.
