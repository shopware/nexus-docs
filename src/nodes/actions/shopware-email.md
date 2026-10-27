---
nav:
  title: "Send Shopware Email"
  position: 26
---

# Send Shopware Email

Use the Send Shopware Email node to send an email through your Shopware shop's mail setup from a workflow. It is a quick way to notify a customer or an internal mailbox when a workflow finishes, for example a confirmation that an order reached the ERP, a low-stock alert to purchasing, or a follow-up to a customer after a return.

Choose a **Connection Method** and, for **Connected shop**, the **Shopware Shop** to send from. Then set the **To Email**, the **Subject** and the **HTML Content**. All three accept expressions such as <code v-pre>{{orderCustomer.email}}</code> so the message can be built from the trigger or the previous node. The node sends one email to one recipient per execution.

:::info
**Node type:** `action.shopware-email` · **Category:** Action
:::

## Credentials

:::info
A **shopware** credential is optional for this node. The steps below explain when you need one.
:::

With **Connected shop**, no credential is needed: link the shop from your Shopware admin (**Settings → System → Nexus** and choose **Link this shop**) and select it under **Shopware Shop**.

With **API credentials**:

1. In your Shopware admin, open **Settings → System → Integrations** and create an integration. Copy the access key ID and secret access key.
2. In Nexus, open **Integrations** and select **Shopware**. Enter the **Shop URL**, the access key ID as **Client ID** and the secret as **Client secret**.
3. Use **Test connection**, then save the credential and select it in the node's **Credentials** tab.

The email is sent by Shopware using the mailer settings of that shop, so the shop must be able to send mail.

## Node parameters

Configure this node with these parameters.

### Connection Method

How to connect to the Shopware shop

- Type: Dropdown
- Required
- Default: `integration`

Options:

- **Connected shop** `integration`
- **API credentials** `credentials`

### Shopware Shop

Select the Shopware shop to send the email from

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Connection Method** is **Connected shop**

### To Email

Email address of the recipient

- Type: Text
- Required

### Subject

Email subject line

- Type: Text
- Required

### HTML Content

HTML email content

- Type: Code / template
- Required

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for email template |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Email sending result |

The node passes its input on unchanged: the output is the data the node received, so the next node can keep working with the order or record. Nothing about the sent email is added.

## Examples

### Confirm to the customer that their order was shipped

Connect a **Shopware Event Trigger** listening for **Delivery status changed to shipped** (ID `state_enter.order_delivery.state.shipped`) to this node:

| Parameter | Value |
| --- | --- |
| Connection Method | Connected shop |
| Shopware Shop | your production shop |
| To Email | <code v-pre>{{orderCustomer.email}}</code> |
| Subject | <code v-pre>Your order {{orderNumber}} is on its way</code> |
| HTML Content | <code v-pre>&lt;p&gt;Hello {{orderCustomer.firstName}},&lt;/p&gt;&lt;p&gt;your order {{orderNumber}} has been shipped.&lt;/p&gt;</code> |

### Alert purchasing when a sync fails

Connect the **False** port of an **If** node that checks the ERP response to this node:

| Parameter | Value |
| --- | --- |
| Connection Method | Connected shop |
| Shopware Shop | your production shop |
| To Email | `purchasing@example.com` |
| Subject | <code v-pre>ERP sync failed for order {{orderNumber}}</code> |
| HTML Content | `<p>The order could not be created in the ERP. Please check it manually.</p>` |

<code v-pre>{{orderNumber}}</code> comes from the trigger, so it resolves in this node even though an ERP node and an **If** node run in between.

## Related resources

- [Shopware: email templates](https://docs.shopware.com/en/shopware-6-en/settings/email-templates)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The email is never received**: the step succeeded, so Shopware accepted the email, but the shop's mailer is not configured or the recipient's server rejected the sender. Check the mailer settings in your Shopware admin and send a test mail from Shopware itself.
- **The node configuration is invalid**: **To Email**, **Subject** or **HTML Content** is empty after the expressions are filled in, usually because a field path does not exist in the data. For orders the customer email is `orderCustomer.email`, not a top-level field. The same message appears when the selected shop has been disconnected from Nexus. If the API credential has no usable **Shop URL**, Nexus shows **Invalid shop URL** instead; correct the URL in the credential under **Integrations**.
- **Authentication failed for the connected service**: with **API credentials**, Shopware refused the access key, typically because the integration was deleted or its secret regenerated. Update the credential under **Integrations**. If Shopware answers that the integration is not allowed to send mail, Nexus shows **Permission denied by the connected service**; check the integration's role in the Shopware admin.
- **The Shopware shop is rate limiting requests. Check the request limits set by the shop or its hosting provider.**: the shop or its hosting throttled Nexus. The node sends once and does not retry, because a retry could deliver the email twice; space out runs or run the workflow again later.
- **The connected service did not respond in time**: Shopware did not answer within the time limit. The email may still have been sent; check the recipient's inbox or the shop's mail log before you run the workflow again.
- **The email shows HTML tags in a plain-text mail client**: the node sends the **HTML Content** as both the HTML and the plain-text part of the email, so clients that display the plain-text part show the markup. Keep the HTML simple so it still reads well as text.
