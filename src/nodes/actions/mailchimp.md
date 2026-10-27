---
nav:
  title: "Mailchimp"
  position: 12
---

# Mailchimp

Use the Mailchimp node to keep a Mailchimp audience in sync with Shopware and to drive Mailchimp automations from shop activity. Typical uses are creating or updating an audience member when a customer registers, tagging a contact after a purchase, recording an order as a member event that starts a journey, and reading a campaign's engagement report.

Pick an **Operation**, then the **Audience** it applies to. **Create or update audience member** identifies the contact by **Contact Email** and writes **Merge Fields**, language and VIP status. **Add or remove member tags** and **Track member event** work on a contact who is already in the audience. **Trigger journey step** fires a Customer Journey API starting point for one contact. **Read campaign report** returns the aggregate report for a **Campaign**.

:::info
**Node type:** `action.mailchimp-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **mailchimp** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Mailchimp, create an API key under Account & billing, Extras, API keys. Create it as an owner or a dedicated service user: Mailchimp deletes every key a user created when that user loses access to the account.
2. In Nexus, open **Integrations** and select **Mailchimp**.
3. Paste the whole key into **API key**. The key ends in a dash plus your data center, for example `-us6`; Nexus derives the API host from it.
4. Use **Test connection**, then save.

The key grants full access to the Mailchimp account; Mailchimp has no read-only or scoped keys.

## Operations

- **Create or update audience member** `upsertMember`
- **Add or remove member tags** `updateMemberTags`
- **Track member event** `trackEvent`
- **Trigger journey step** `triggerJourney`
- **Read campaign report** `getCampaignReport`

## Node parameters

Configure this node with these parameters.

### Operation

Mailchimp operation to execute

- Type: Dropdown
- Required
- Default: `upsertMember`

Options:

- **Create or update audience member** `upsertMember`
- **Add or remove member tags** `updateMemberTags`
- **Track member event** `trackEvent`
- **Trigger journey step** `triggerJourney`
- **Read campaign report** `getCampaignReport`

### Audience

Mailchimp audience the contact, tags or event is written to. Audience IDs are account-specific.

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Operation** is one of **Create or update audience member**, **Add or remove member tags**, **Track member event**

### Custom Audience ID

Mailchimp audience ID, used when the audience list cannot be loaded.

- Type: Text
- Required
- Placeholder: `e.g. a1b2c3d4e5`
- Shown when **Operation** is one of **Create or update audience member**, **Add or remove member tags**, **Track member event** and **Audience** is **__custom__**

### Contact Email

Email address identifying the Mailchimp contact. Mailchimp addresses contacts by email. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Placeholder: <code v-pre>{{email}}</code>
- Shown when **Operation** is one of **Create or update audience member**, **Add or remove member tags**, **Track member event**, **Trigger journey step**

### Status For New Contacts

Subscription status applied only to a contact Mailchimp has not seen before. Transactional stores the contact without claiming consent. Mailchimp bills unsubscribed and transactional contacts against the plan's contact quota.

- Type: Dropdown
- Required
- Default: `transactional`
- Shown when **Operation** is **Create or update audience member**

Options:

- **Transactional (no marketing consent)** `transactional`
- **Subscribed (has given consent)** `subscribed`
- **Pending (double opt-in)** `pending`
- **Unsubscribed** `unsubscribed`

### Status For Existing Contacts

Optional subscription status applied to an existing contact. Leave empty to keep the contact's current status. Mailchimp refuses to resubscribe a contact who unsubscribed themselves.

- Type: Dropdown
- Optional
- Shown when **Operation** is **Create or update audience member**

Options:

- **Subscribed** `subscribed`
- **Unsubscribed** `unsubscribed`
- **Pending (double opt-in)** `pending`

### Merge Fields

Mailchimp merge fields as JSON, keyed by merge tag. Every tag must already exist on the audience; the connector rejects an unknown tag rather than letting Mailchimp discard the value. FNAME, LNAME, ADDRESS and PHONE exist on every new audience.

- Type: JSON
- Optional
- Placeholder: <code v-pre>{"FNAME": "{{firstName}}", "LNAME": "{{lastName}}"}</code>
- Shown when **Operation** is **Create or update audience member**

### Language

Optional contact language as a Mailchimp language code. Shopware's languageId is a UUID and cannot be used directly.

- Type: Text
- Optional
- Placeholder: `e.g. de`
- Shown when **Operation** is **Create or update audience member**

### Mark As VIP

Sets the contact's VIP flag. Leave unset to keep whatever the Mailchimp account already has: enabling and then disabling this writes the flag off again.

- Type: Toggle
- Optional
- Shown when **Operation** is **Create or update audience member**

### Tags

Tags to declare on the contact as JSON. Status active adds a tag, inactive removes it; tags not listed are left alone. Mailchimp creates a tag that does not exist yet.

- Type: JSON
- Required
- Placeholder: `[{"name": "purchased", "status": "active"}, {"name": "prospect", "status": "inactive"}]`
- Shown when **Operation** is **Add or remove member tags**

### Event Name

Name of the member event, 2 to 30 characters. Mailchimp journeys listen for this name.

- Type: Text
- Required
- Placeholder: `shopware_order_placed`
- Shown when **Operation** is **Track member event**
- Validated against pattern `^.{2,30}$`

### Event Properties

Optional event properties as JSON. Mailchimp stores property values as strings only, so numbers are converted; nested objects and lists are rejected.

- Type: JSON
- Optional
- Placeholder: <code v-pre>{"order_number": "{{orderNumber}}", "order_total": "{{amountTotal}}"}</code>
- Shown when **Operation** is **Track member event**

### Occurred At

Optional event timestamp in ISO 8601 format. Defaults to when Mailchimp receives the event.

- Type: Text
- Optional
- Placeholder: `2026-08-04T10:15:00Z`
- Shown when **Operation** is **Track member event**

### Suppress Automations

Marks the write as a bulk sync so Mailchimp does not fire automations based on it. Leave disabled for merchant-facing workflows that are meant to trigger journeys.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Add or remove member tags**, **Track member event**

### Journey ID

Journey ID from the Automation flow builder. Add a Customer Journey API starting point to the journey; Mailchimp shows the journey and step IDs there. Mailchimp has no API that lists journeys, so both IDs are entered by hand.

- Type: Text
- Required
- Placeholder: `e.g. 8434`
- Shown when **Operation** is **Trigger journey step**

### Journey Step ID

Step ID of the Customer Journey API starting point, from the same URL as the journey ID.

- Type: Text
- Required
- Placeholder: `e.g. 26`
- Shown when **Operation** is **Trigger journey step**

### Campaign

Mailchimp campaign whose engagement report is read.

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Operation** is **Read campaign report**

### Custom Campaign ID

Mailchimp campaign ID, used when the campaign list cannot be loaded.

- Type: Text
- Required
- Placeholder: `e.g. b1c2d3e4f5`
- Shown when **Operation** is **Read campaign report** and **Campaign** is **__custom__**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Mailchimp templating, merge fields, tags or event properties |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Mailchimp operation result |

**Create or update audience member** returns the member's `email`, `subscriberHash`, `status`, `listId` and `webId`, merged into the incoming record, so the node directly after it still sees the original fields. **Add or remove member tags**, **Track member event** and **Trigger journey step** return no body from Mailchimp; the input record is passed through. **Read campaign report** puts the campaign report object into the output.

## Examples

### Add a customer to the audience without claiming consent

Connect a **Shopware Event Trigger** listening for **Customer created or updated** to this node and configure:

| Parameter | Value |
| --- | --- |
| Operation | Create or update audience member |
| Audience | pick the audience from the dropdown |
| Contact Email | <code v-pre>{{data.email}}</code> |
| Status For New Contacts | Transactional (no marketing consent) |
| Merge Fields | <code v-pre>{"FNAME": "{{data.firstName}}", "LNAME": "{{data.lastName}}"}</code> |

### Record a purchase event that starts a journey

Chain a **Create or update audience member** step first, then:

| Parameter | Value |
| --- | --- |
| Operation | Track member event |
| Audience | pick the audience from the dropdown |
| Contact Email | <code v-pre>{{data.orderCustomer.email}}</code> |
| Event Name | `shopware_order_placed` |
| Event Properties | <code v-pre>{"order_number": "{{data.orderNumber}}", "order_total": "{{data.amountTotal}}"}</code> |

## Related resources

- [Mailchimp add or update a list member](https://mailchimp.com/developer/marketing/api/list-members/add-or-update-list-member/)
- [Mailchimp merge fields](https://mailchimp.com/developer/marketing/docs/merge-fields/)
- [Mailchimp track outside activity events](https://mailchimp.com/developer/marketing/guides/track-outside-activity-events/)
- [Mailchimp: Add or remove member tags](https://mailchimp.com/developer/marketing/api/list-member-tags/add-or-remove-member-tags/)
- [Mailchimp: Add a member event](https://mailchimp.com/developer/marketing/api/list-member-events/add-event/)
- [Mailchimp: Trigger a Customer Journey step](https://mailchimp.com/developer/marketing/api/customer-journeys-journeys-steps-actions/trigger-journeys-steps-actions/)
- [Mailchimp: Get a campaign report](https://mailchimp.com/developer/marketing/api/reports/get-campaign-report/)
- [Mailchimp: About API keys](https://mailchimp.com/help/about-api-keys/)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The requested resource was not found**: on **Add or remove member tags** and **Track member event**, the contact is not in the **Audience**, or the audience is wrong (Mailchimp reports both the same way). These operations never create a contact; put a **Create or update audience member** step in front. On other operations, the **Audience**, **Campaign** or **Journey ID** does not exist or was deleted or archived.
- **The node configuration is invalid**: Nexus stopped the step before calling Mailchimp. Common causes: a key in **Merge Fields** that does not exist on the audience (create the merge field in Mailchimp first), a **Tags** entry without a name or with a status other than `active` or `inactive`, an **Event Name** shorter than 2 or longer than 30 characters, an **Event Properties** value that is a nested object or list (Mailchimp stores event properties as plain strings; numbers are converted), an **Occurred At** that is not an ISO 8601 timestamp such as `2026-08-04T10:15:00Z`, or an empty **Contact Email**, **Journey ID** or **Journey Step ID**.
- **The node received data it could not process**: either an expression in a field such as **Contact Email** points at a field the input does not have (Nexus refuses to send the value empty), or Mailchimp rejected a value in the record, for example an invalid email address or a merge-field value that does not fit the field's type in the audience.
- **The connected service rejected the request**: most often the contact unsubscribed themselves, and Mailchimp does not allow an integration to subscribe them again; they have to opt in through a Mailchimp signup form. This is never retried. Remove the subscribe step for such contacts. It also appears when Mailchimp refuses an action for the contact's current status.
- **Authentication failed for the connected service**: the API key is invalid or was revoked. Mailchimp also deletes a user's keys when that user loses access to the account. Create a new key under an owner or service user and update the credential under **Integrations**.
- **The connected service is rate limiting requests**: Mailchimp allows 10 simultaneous requests per account, shared with every other integration using it. **Create or update audience member**, **Add or remove member tags** and **Read campaign report** wait and retry automatically; **Track member event** and **Trigger journey step** run once so they never start an automation twice.
- **A journey started but no email was sent**: Mailchimp only emails subscribed contacts. A contact stored as transactional moves through the flow but skips its email steps until they opt in.
