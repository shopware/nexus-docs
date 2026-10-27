---
nav:
  title: "Send Slack Message"
  position: 27
---

# Send Slack Message

Use the Send Slack Message node to post a message to a Slack channel from a workflow. It is the simplest way to notify a team when something happens in Shopware, for example when a new order arrives or an ERP sync fails.

The node sends one text message per execution. Use expressions such as <code v-pre>{{data.orderNumber}}</code> in the message to include data from the trigger or the previous node.

:::info
**Node type:** `action.slack-message` · **Category:** Action
:::

## Credentials

:::info
This node uses a **slack** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **Slack**.
2. Choose **Connect** to authorise Nexus with your Slack workspace through Slack's OAuth flow.
3. Invite the Nexus app to every channel you want to post to. Slack rejects messages to channels the app has not joined.

After the credential is saved, the **To channel** dropdown in the node lists the channels the app can see.

## Node parameters

Configure this node with these parameters.

### To channel

Slack channel to send the message to

- Type: Dropdown
- Required
- Options are loaded from your connected account

### Message

Slack message

- Type: Code / template
- Required

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Slack message |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Slack message sending result |

The node passes the data it received through unchanged, so the node after it sees the same fields as this one did. Slack's response, such as the message timestamp, is not added to the output.

## Examples

### Notify a channel about a new order

Connect a **Shopware Event Trigger** listening for **Order placed** (ID `checkout.order.placed`) to this node and configure:

| Parameter | Value |
| --- | --- |
| To channel | `#orders` |
| Message | <code v-pre>New order {{data.orderNumber}} for {{data.amountTotal}} EUR</code> |

## Related resources

- [Slack: chat.postMessage](https://api.slack.com/methods/chat.postMessage)
- [Slack: formatting messages](https://api.slack.com/reference/surfaces/formatting)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: Slack refused the channel. Slack reports this as the channel not existing, the Nexus app not being a member of the channel, or the channel being archived. Invite the Nexus app to the channel in Slack, or pick an active channel in **To channel**. The same message appears when the Slack credential is missing or cannot be read; re-select or re-authorise it under **Integrations**.
- **The node failed unexpectedly**: Slack answered with an error that Nexus does not classify, most often because the access was revoked or the Nexus app was removed from the workspace (Slack reports reasons such as invalid authentication or a revoked token). Re-authorise the Slack credential under **Integrations** and run the workflow again.
- **Authentication failed for the connected service**: Slack rejected the token at the HTTP level. Re-authorise the Slack credential under **Integrations**.
- **The connected service is rate limiting requests**: Slack allows roughly one message per second per channel. Sending a message is not retried, so a burst fails the step. Add a **Delay** node or combine several events into one message.
- **The connected service is currently unavailable**: Slack returned a server error. The send is not retried automatically; run the workflow again once Slack is available.
