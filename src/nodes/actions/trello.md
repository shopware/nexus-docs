---
nav:
  title: "Trello API"
  position: 32
---

# Trello API

Use the Trello API node to read Trello boards, lists and cards and to create, update or comment on cards from a workflow. It suits teams that run fulfilment, returns or content work on a Trello board: create a card when a Shopware order needs manual review, move it to another list when the order ships, and add a comment when a sync with another system fails.

Pick an **Entity**, then an **Operation**. Boards and lists are read-only (**Get**, **Get many**); cards can be read, created and updated; comments can be listed and created. Identifiers such as **Board ID**, **List ID** and **Card ID** can be typed in or filled with expressions, and **Additional Fields** lets you pass any other Trello request parameter as JSON. On **Update**, **Archive Card** is only sent when it is on, so leaving it off never changes the archived state; to restore an archived card pass `{"closed": false}` in **Additional Fields**.

:::info
**Node type:** `action.trello-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **trello** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Trello, create a Power-Up in the Power-Up admin (trello.com/power-ups/admin) and copy its API key.
2. In Nexus, open **Integrations** and select **Trello**.
3. Enter the key in **API key**.
4. Add the Nexus origin shown in the dialog to the API key's allowed origins list in Trello and confirm the checkbox. Trello only hands the token back to an origin on that list.
5. Choose **Authorize**. Trello opens in a new window, you grant read and write access, and the token is filled into **API token** automatically. You can also paste a token you created yourself.
6. Use **Test connection** to verify the pair before saving.

A Trello token does not expire on its own; it stops working only when revoked in Trello. Everything the node does is done as the Trello member who authorised it, so a shared service account is a good choice for automated workflows.

## Operations

- **Get** `get` (only when **Entity** is one of **Board**, **Card**)
- **Get many** `get_many`
- **Create** `create` (only when **Entity** is one of **Card**, **Comment**)
- **Update** `update` (only when **Entity** is **Card**)

## Node parameters

Configure this node with these parameters.

### Entity

Trello entity to operate on. The operation field follows the supported actions for the selected entity.

- Type: Dropdown
- Required
- Default: `card`

Options:

- **Board** `board`
- **List** `list`
- **Card** `card`
- **Comment** `comment`

### Operation

Trello action to execute. Supported actions depend on the selected entity.

- Type: Dropdown
- Required
- Default: `get_many`

Options:

- **Get** `get` (only when **Entity** is one of **Board**, **Card**)
- **Get many** `get_many`
- **Create** `create` (only when **Entity** is one of **Card**, **Comment**)
- **Update** `update` (only when **Entity** is **Card**)

### Member ID

Trello member whose boards are listed. Use me for the account bound to the credential.

- Type: Text
- Optional
- Default: `me`
- Placeholder: `me`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Board** and **Operation** is **Get many**

### Board ID

Trello board to read. If empty, the connector reads boardId, idBoard, or id from the payload.

The board ID is the 24-character value in the board URL, or the short link shown after /b/ in that URL.

- Type: Text
- Optional
- Placeholder: <code v-pre>{{boardId}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Board** and **Operation** is **Get** or **Entity** is one of **List**, **Card** and **Operation** is **Get many**

### Card ID

Trello card the operation works on: the card read or written, or the card carrying the comments. If empty, the connector reads cardId, idCard, or id from the payload.

- Type: Text
- Optional
- Placeholder: <code v-pre>{{cardId}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Get**, **Update** or **Entity** is **Comment** and **Operation** is one of **Get many**, **Create**

### List ID

Trello list the card belongs to. Required when creating a card; on update it moves the card to that list. If empty, the connector reads listId or idList from the payload.

- Type: Text
- Optional
- Placeholder: <code v-pre>{{listId}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Create**, **Update**

### Card Name

Title shown on the Trello card.

- Type: Text
- Optional
- Placeholder: <code v-pre>Order {{orderNumber}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Create**, **Update**

### Card Description

Card description. Trello renders this field as Markdown, so headings, lists, and links are kept.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Create**, **Update**

### Due Date

Card due date as an ISO 8601 timestamp. Leave empty to keep the card without a due date.

- Type: Text
- Optional
- Placeholder: `2026-09-30T17:00:00Z`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Create**, **Update**

### Position

Card position within its list: top, bottom, or a positive number.

- Type: Text
- Optional
- Placeholder: `top`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Create**, **Update**

### Member IDs

Comma-separated Trello member IDs assigned to the card. On update this replaces the current assignees.

- Type: Text
- Optional
- Placeholder: `5f2b8c2d3e4a4b5c8d9e0f1a,5f2b8c2d3e4a4b5c8d9e0f1b`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Create**, **Update**

### Label IDs

Comma-separated Trello label IDs applied to the card. On update this replaces the current labels.

- Type: Text
- Optional
- Placeholder: `5f2b8c2d3e4a4b5c8d9e0f1c`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Card** and **Operation** is one of **Create**, **Update**

### Archive Card

Archive the card instead of leaving it on the board. Archived cards stay searchable in Trello and can be restored.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Entity** is **Card** and **Operation** is **Update**

### Comment

Comment body added to the card. Trello renders it as Markdown. If empty, the connector reads text or commentText from the payload.

- Type: Code / template
- Optional
- Placeholder: <code v-pre>Fulfilled by workflow run {{runId}}</code>
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Entity** is **Comment** and **Operation** is **Create**

### Page Limit

Maximum Trello resources to request per page

- Type: Number
- Optional
- Default: `50`
- Shown when **Operation** is **Get many**

### Additional Fields

Extra Trello request parameters merged into the request. Values here override the fields above.

Please read the payload from [Trello apidoc](https://developer.atlassian.com/cloud/trello/rest)

- Type: JSON
- Optional
- Placeholder: `{"idCardSource":"5f2b8c2d3e4a4b5c8d9e0f1a"}`
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is one of **Create**, **Update**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data or payload for the Trello operation |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Trello API operation result |

Every operation returns the same envelope: `entity`, `operation`, `count` and `hasMore`, plus the Trello record under `data` for single-record operations or the records under `items` for **Get many**. For single records, `id`, `url` and `shortUrl` are also copied to the top level so the next node can reference <code v-pre>{{url}}</code> directly.

## Examples

### List the boards of the connected member

Read every board the credential's Trello account can see.

```json
{
  "entity": "board",
  "operation": "get_many",
  "memberId": "me"
}
```

### Create a card

Add a card to a list with a name and a description.

```json
{
  "entity": "card",
  "operation": "create",
  "listId": "5f2b8c2d3e4a4b5c8d9e0f1a",
  "name": "Order 10042",
  "desc": "Created from the fulfilment workflow"
}
```

### Comment on a card

Append a comment to an existing card.

```json
{
  "entity": "comment",
  "operation": "create",
  "cardId": "5f2b8c2d3e4a4b5c8d9e0f1b",
  "commentText": "Shipment handed to the carrier"
}
```

### Create a review card from a Shopware order

Connect a **Shopware Event Trigger** listening for **Order placed**, add an **If** node with your review condition, and configure this node on the true branch:

| Parameter | Value |
| --- | --- |
| Entity | Card |
| Operation | Create |
| List ID | `64f1c2d3e4a5b6c7d8e9f0a1` |
| Card Name | <code v-pre>Review order {{data.orderNumber}}</code> |
| Card Description | <code v-pre>Total {{data.amountTotal}} EUR. Check payment and shipping address before releasing the order.</code> |
| Due Date | <code v-pre>{{data.orderDateTime}}</code> |
| Position | `top` |

The list ID is the `id` of the list in Trello; the **Get many** operation on the **List** entity returns it for every list on a board.

## Related resources

- [Trello REST API reference](https://developer.atlassian.com/cloud/trello/rest)
- [Trello API key and token](https://developer.atlassian.com/cloud/trello/guides/rest-api/api-introduction/)
- [Trello: Authorizing a client and obtaining a token](https://developer.atlassian.com/cloud/trello/guides/rest-api/authorization/)
- [Trello: API rate limits](https://developer.atlassian.com/cloud/trello/guides/rest-api/rate-limits/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Authentication failed for the connected service**: the Trello token was revoked. Trello cannot refresh it; open the credential under **Integrations** and choose **Authorize again**.
- **Permission denied by the connected service**: the token works, but the Trello member who authorised it has no access to that board, list or card. Add the member to the board, or reconnect with a member who has access.
- **The requested resource was not found**: the **Board ID**, **List ID** or **Card ID** is wrong or the record was deleted. Use the `id` returned by a **Get** or **Get many** operation.
- **The node configuration is invalid**: a setting cannot be sent to Trello. Common causes: **Additional Fields** is not a flat JSON object or contains `key` or `token`, **Due Date** is not an ISO 8601 timestamp such as `2026-09-30T17:00:00Z`, or an **Update** has no field to change. To clear a field such as the due date, pass `{"due": null}` in **Additional Fields**.
- **The connected service is rate limiting requests**: Trello allows 100 requests per 10 seconds per token, and every workflow that uses the same credential shares that budget. Reads wait and try again; creates and updates are not retried. Add a Delay node or aggregate records before writing.
- **A card or comment was created twice, or a write failed but the card exists**: Trello has no way to deduplicate writes, so a create or update is never retried after a timeout, a server error or a rate-limit response. Check the board before re-running the workflow.
- **Get many returns fewer records than exist**: results are cut to **Page Limit**, and `hasMore` tells you more were available. It is not a cursor; raise the limit to read more.
