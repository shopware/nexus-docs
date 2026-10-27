---
nav:
  title: "ERPNext Event Trigger"
  position: 1
---

# ERPNext Event Trigger

Use the ERPNext Event Trigger node to start a workflow from a document event in ERPNext, for example a submitted Sales Order, a cancelled Sales Invoice, or an updated Item. It lets ERPNext drive Shopware: update stock or prices when an Item changes, or mark a Shopware order as shipped when a Delivery Note is submitted.

Enter the **DocType** exactly as ERPNext names it (`Sales Order`, `Item`, a custom DocType), choose the **Document Event** to listen for (on submit, on update, after insert, on cancel, on trash), and set a **Connection Source ID** of at least 24 characters that is unique to this ERPNext connection. Events reach Nexus through a Frappe Webhook you create in ERPNext; the setup details are shown after you activate the workflow.

:::info
**Node type:** `trigger.erpnext_webhook` · **Category:** Trigger
:::

## Credentials

The trigger does not use a saved credential. Instead, you create a Frappe Webhook in ERPNext that posts to Nexus:

1. Configure the node, publish the workflow and activate it. Nexus then shows a **Configure your ERPNext webhook** panel with the **Request URL**, the **Webhook secret** and the **Request headers** to use.
2. In ERPNext, open **Integrations → Webhook** and create a new Webhook. Set **DocType** and **Doc Event** to the same values as the node.
3. Set the **Request URL** and the request method to POST, enable the security option and paste the **Webhook secret** from Nexus. ERPNext signs each request with it and Nexus rejects requests whose signature does not match.
4. Add the three request headers shown in Nexus: `X-Nexus-ERPNext-Source-ID` (your Connection Source ID), `X-Nexus-Tenant-ID` and `X-Nexus-Event-Type` (`<DocType>/<docEvent>`, for example `Sales Order/on_submit`).
5. Set the request structure to JSON and use this body so the whole document is sent under `data`:

```json
{
  "doctype": "{{ doc.doctype }}",
  "name": "{{ doc.name }}",
  "docstatus": {{ doc.docstatus }},
  "data": {{ doc | tojson }}
}
```

Create one Frappe Webhook per DocType and event. Webhooks for the same connection share the Request URL, secret and source/tenant headers; only the `X-Nexus-Event-Type` header differs.

## Node parameters

Configure this node with these parameters.

### DocType

Any ERPNext DocType whose events should trigger this workflow, e.g. Sales Order, Sales Invoice, Payment Entry, Customer, Item, or a custom DocType like Purchase Order. Must match the Frappe Webhook's DocType exactly.

- Type: Text
- Required
- Placeholder: `Sales Order`

### Document Event

Frappe document lifecycle event to listen for

- Type: Dropdown
- Required
- Default: `on_submit`

Options:

- **On submit** `on_submit`
- **On update** `on_update`
- **After insert** `after_insert`
- **On cancel** `on_cancel`
- **On trash (delete)** `on_trash`

### Connection Source ID

A unique identifier for this ERPNext connection (min 24 chars). Set the same value in your Frappe Webhook's X-Nexus-ERPNext-Source-ID header. The signing secret is derived per connection and shown after you activate the workflow, so this value need not be secret, but keep it unique per connection and avoid reusing a human-readable label.

- Type: Text
- Required

## Input and output

### Input

None.

### Output

| Port | Type | Description |
| --- | --- | --- |
| Data | `object` | ERPNext event envelope. With the recommended webhook body, the full document is available under data.data (reference downstream as <code v-pre>{{data.data.&amp;lt;field&gt;}}</code>) |

The output is an event envelope. Nexus stores the whole request body under `data`, and the recommended webhook body puts the ERPNext document under its own `data` key, so document fields are at `data.data`. Reference them as <code v-pre>{{data.data.&lt;field&gt;}}</code>, for example <code v-pre>{{data.data.item_code}}</code> for an Item. The top-level `doctype`, `name` and `docstatus` of the body are at <code v-pre>{{data.doctype}}</code>, <code v-pre>{{data.name}}</code> and <code v-pre>{{data.docstatus}}</code>. If the node directly before the one using the expression returns its own `data` field, its value wins over the trigger's, so copy the document fields you need with a **Field Mapping** node directly after the trigger.

## Examples

### Update Shopware stock when an ERPNext Item changes

| Parameter | Value |
| --- | --- |
| DocType | `Item` |
| Document Event | On update |
| Connection Source ID | `erp-prod-6f1c9d2a4b7e8c3f5a1d` |

Connect the trigger to a **Field Mapping** node that maps the source field `data.data.item_code` to `productNumber`, then a **Shopware API Call** with **Entity Type** Product and **Operation** Update existing only.

## Related resources

- [Frappe: Webhooks](https://docs.frappe.io/framework/user/en/guides/integration/webhooks)
- [Frappe: REST API](https://docs.frappe.io/framework/user/en/api/rest)

## Common issues

- **The workflow never fires**: the most common causes are a DocType or Doc Event that does not match the node, a missing or mistyped `X-Nexus-*` header, or a wrong secret. Requests that fail verification are rejected without reaching the workflow, so compare every value against the setup panel in Nexus.
- **The workflow stopped firing after you changed the Connection Source ID**: the secret is tied to the source ID. Copy the new secret from the setup panel into the Frappe Webhook.
- **Document fields are empty in the workflow**: with the JSON body above, the document is under `data.data`, so <code v-pre>{{data.item_code}}</code> is empty and <code v-pre>{{data.data.item_code}}</code> works. Only `name`, `doctype` and `docstatus` also sit at the top of the body. If you used a different body, the payload under `data` contains exactly what your Webhook Data template sends.
- **Two workflows run for one ERPNext event**: two workflows share the same Connection Source ID, DocType and event. Use a distinct source ID per connection and one workflow per event type.
- **Documents changed while the webhook was misconfigured never start the workflow**: Nexus does not keep requests it rejects, so those events are lost. For reconciliation or finance flows where a missed document matters, add a **Schedule Trigger** workflow that reads recent documents with the **ERPNext** node's **List** operation, filtered on `modified`.
