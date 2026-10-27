---
nav:
  title: "Shopware Event Trigger"
  position: 3
---

# Shopware Event Trigger

Use the Shopware Event Trigger node to start a workflow when something happens in your connected Shopware shop, for example an order is placed, a payment status changes, or a product is created or updated. It is the starting point for most Shopware-centred automations: notify a team, push the order into an ERP, or update a record in another system.

Pick the **Shopware Shop**, then an **Event Category** to narrow the list, then the **Event** itself. The shop and category must be selected before the event list loads. The node has no inputs. Before the first node runs, Nexus loads the record the event refers to, so its fields are available as expressions in any node of the workflow.

:::info
**Node type:** `trigger.shopware_webhook` · **Category:** Trigger
:::

## Credentials

1. In your Shopware admin, open **Settings → System → Nexus** and choose **Link this shop** to connect the shop to your Nexus workspace.
2. In Nexus, the linked shops appear in the **Shopware Shop** dropdown of this node. No separate credential is needed for the trigger.

## Node parameters

Configure this node with these parameters.

### Shopware Shop

Select the shop to listen to events from (by shop ID)

- Type: Dropdown
- Required
- Options are loaded from your connected account

### Event Category

Choose a category to narrow down the list of available events

- Type: Dropdown
- Required
- Options are loaded from your connected account

### Event

Select the specific event that will trigger this workflow

- Type: Dropdown
- Required
- Options are loaded from your connected account

## Input and output

### Input

None.

### Output

| Port | Type | Description |
| --- | --- | --- |
| Data | `object` | Webhook output data |

What the trigger provides depends on the kind of event:

- **Order placed** (ID `checkout.order.placed`) and the "… created or updated" events, such as **Product created or updated** (ID `product.written`), provide the record itself. Reference its fields directly, for example <code v-pre>{{orderNumber}}</code> or <code v-pre>{{amountTotal}}</code> for an order. When Shopware reports several records in one event, the output is a list of them.
- Status-change events, such as **Payment status changed to paid** (ID `state_enter.order_transaction.state.paid`), provide `transition`, `toState`, `fromState` and the order under `entityData`, and copy the order's fields to the top level, so <code v-pre>{{orderNumber}}</code> works here too.
- The "… deleted" events, such as **Product deleted** (ID `product.deleted`), only carry IDs, because the record no longer exists. Use <code v-pre>{{entityId}}</code> to find the matching record in another system.

## Examples

### Notify a team about a new order

| Parameter | Value |
| --- | --- |
| Shopware Shop | your production shop |
| Event Category | Orders |
| Event | Order placed |

Connect the node to a **Send Slack Message** node with the message <code v-pre>New order {{orderNumber}} for {{amountTotal}} EUR</code>.

### React to a paid order

| Parameter | Value |
| --- | --- |
| Shopware Shop | your production shop |
| Event Category | Payments & transactions |
| Event | Payment status changed to paid |

Follow this with a **Field Mapping** node and an ERP action to create the sales order only once payment is confirmed.

## Related resources

- [Shopware: connecting a shop to Nexus](https://docs.shopware.com/en/shopware-6-en/shopware-services/shopware-nexus)
- [Shopware: webhooks and business events](https://developer.shopware.com/docs/guides/plugins/apps/webhook.html)

## Common issues

- **The Shopware Shop dropdown is empty**: the shop is not linked yet. Link it from the Shopware admin (**Settings → System → Nexus**, **Link this shop**) and reload the workflow.
- **The selected Shopware shop does not belong to your company. Please select a shop that your company owns.**: the workflow references a shop owned by another company, typically after an import. Select one of your own shops before publishing.
- **The node configuration is invalid**: for a "… created or updated" event, Nexus could not load the record because it was deleted in Shopware before the workflow started, the shop was disconnected from Nexus after the workflow was published, or the shop's link to Nexus is incomplete. Re-link the shop if the problem persists for records that still exist. If the linked shop has no usable address, Nexus shows **Invalid shop URL** instead.
- **A field is empty in the workflow**: different events carry different data. An order field is not available on a product event, and the deleted events carry only IDs. For deleted and status-change events the field picker lists the event fields (`entityId`, `transition`, `toState`, `fromState`, `entityData`) rather than the order's fields; for status-change events you can still use order fields such as <code v-pre>{{orderNumber}}</code>.
- **The workflow ran twice for the same event**: Shopware delivers events at least once. Nexus drops repeated deliveries it can recognise, but design the following steps to be safe on repeat, for example by using create-or-update operations keyed on the order number.
