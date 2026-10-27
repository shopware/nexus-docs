---
nav:
  title: "Zoho CRM"
  position: 33
---

# Zoho CRM

Use the Zoho CRM node to create and update Zoho CRM records from a workflow. It writes leads, contacts, accounts, deals, tasks and notes, so you can turn Shopware registrations into leads or contacts, create a deal for each Shopware order and move it through a pipeline, or create follow-up tasks for your sales team.

Pick a **Module** (Leads, Contacts, Accounts, Deals, Tasks or Notes), then an **Operation**. Create is available for every module; Update by Zoho record ID for leads, contacts, accounts and deals; Upsert with one duplicate identity for leads, contacts and accounts; and Update Deal by external ID for deals. The remaining fields depend on the module: a **Layout** for all modules except Notes, a **Deal Pipeline** and stage for deals, an **Identity Field** for upserts and external-ID updates, and owner, status and due-date settings for tasks. The record's field values come from the node input, so map them with a **Field Mapping** node first.

:::info
**Node type:** `action.zoho-crm-api` · **Category:** Action
:::

:::warning
**Experimental**
This node is experimental and its behaviour may change.
:::

## Credentials

:::info
This node uses a **zoho-crm** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **Zoho CRM**.
2. Choose **Continue to Zoho** and sign in as a Zoho CRM administrator or a dedicated integration user. That user authorises Nexus for one Zoho organisation; Nexus detects the Zoho data centre region from the callback, and you never paste a token.
3. Use **Test connection** to confirm the connection is valid for your Zoho environment.

If the authorising user loses access or revokes consent in Zoho, reauthorise the connection under Integrations. **Disconnect** stops Nexus using the connection immediately and asks Zoho to revoke its refresh token.

## Operations

- **Create** `create`
- **Update by Zoho record ID** `update` (only when **Module** is one of **Leads**, **Contacts**, **Accounts**, **Deals**)
- **Upsert with one duplicate identity** `upsert` (only when **Module** is one of **Leads**, **Contacts**, **Accounts**)
- **Update Deal by external ID** `updateByExternalId` (only when **Module** is **Deals**)

## Node parameters

Configure this node with these parameters.

### Module

Zoho CRM module to write

- Type: Dropdown
- Required
- Default: `Contacts`

Options:

- **Leads** `Leads`
- **Contacts** `Contacts`
- **Accounts** `Accounts`
- **Deals** `Deals`
- **Tasks** `Tasks`
- **Notes** `Notes`

### Operation

Bounded write operation supported for the selected module

- Type: Dropdown
- Required
- Default: `create`

Options:

- **Create** `create`
- **Update by Zoho record ID** `update` (only when **Module** is one of **Leads**, **Contacts**, **Accounts**, **Deals**)
- **Upsert with one duplicate identity** `upsert` (only when **Module** is one of **Leads**, **Contacts**, **Accounts**)
- **Update Deal by external ID** `updateByExternalId` (only when **Module** is **Deals**)

### Zoho Record ID

Exact Zoho record ID for update operations

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update by Zoho record ID**

### Identity Mode

Use an immutable Shopware source ID by default; Email matching requires explicit compatibility acceptance

- Type: Dropdown
- Optional
- Default: `sourceId`
- Shown when **Module** is one of **Leads**, **Contacts** and **Operation** is **Upsert with one duplicate identity**

Options:

- **Shopware source ID** `sourceId`
- **Email compatibility mode** `emailCompatibility`

### Identity Field

Metadata-verified unique or organization-based external field that receives the reserved Shopware identity

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Module** is one of **Leads**, **Contacts** and **Operation** is **Upsert with one duplicate identity** and **Identity Mode** is **Shopware source ID** or **Module** is **Accounts** and **Operation** is **Upsert with one duplicate identity** or **Module** is **Deals** and **Operation** is **Update Deal by external ID**

### Layout

Stable Zoho layout ID used for field readiness and write validation

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Module** is one of **Leads**, **Contacts**, **Accounts**, **Deals**, **Tasks**

### Deal Pipeline

Stable pipeline ID for Deal creation or stage updates

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Module** is **Deals** and **Operation** is one of **Create**, **Update Deal by external ID**

### Initial Deal Stage

Stable Stage ID resolved within the selected pipeline for newly created Deals

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Module** is **Deals** and **Operation** is **Create**

### Target Deal Stage

Stable Stage ID resolved within the selected pipeline for an exact stage update

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Module** is **Deals** and **Operation** is **Update Deal by external ID**

### Record Automation

Explicitly keep Zoho's automation default or suppress documented record automations

- Type: Dropdown
- Required
- Default: `providerDefault`
- Shown when **Module** is one of **Leads**, **Contacts**, **Accounts**, **Deals**, **Tasks**

Options:

- **Use Zoho default** `providerDefault`
- **Suppress all record automation** `suppressAll`

### Task Owner

Active and confirmed Zoho user who owns the Task

- Type: Dropdown
- Optional
- Options are loaded from your connected account
- Shown when **Module** is **Tasks**

### Task Status

Status value from the selected Task layout

- Type: Dropdown
- Optional
- Options are loaded from your connected account
- Shown when **Module** is **Tasks**

### Task Due Date

Either omit Due_Date or derive it deterministically from the workflow event time

- Type: Dropdown
- Required
- Default: `omit`
- Shown when **Module** is **Tasks**

Options:

- **Omit due date** `omit`
- **Days after event** `daysAfterEvent`

### Days Until Due

Whole days added to the workflow event time

- Type: Number
- Optional
- Shown when **Module** is **Tasks** and **Task Due Date** is **Days after event**

### Task Notifications

Explicitly use Zoho's default Task owner email and reminder behavior, separately from record automation

- Type: Dropdown
- Required
- Default: `providerDefault`
- Shown when **Module** is **Tasks**

Options:

- **Use Zoho default** `providerDefault`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Mapped CRM record data and normalized output from preceding Zoho CRM actions |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Normalized Zoho CRM result with safe per-module record metadata |

The output is a `zoho` object. `zoho.current` describes this write: `module`, `recordId` and `action` (`insert` or `update`), plus `identityField` when one was used, so the next node can reference <code v-pre>{{zoho.current.recordId}}</code>. The same entry is also listed under `zoho.records.<module>` (for example `zoho.records.contacts`), merged with any `zoho.records` entries already present in this node's input. Zoho's raw response is not returned.

## Examples

### Create or update a contact from a Shopware customer

Map the customer to Zoho field names (for example `Last_Name`, `First_Name`, `Email`) with a **Field Mapping** node, then configure:

| Parameter | Value |
| --- | --- |
| Module | Contacts |
| Operation | Upsert with one duplicate identity |
| Layout | Standard |
| Identity Mode | Shopware source ID |
| Identity Field | a custom unique field on the Contacts module, for example `Shopware_Customer_ID` |
| Record Automation | Use Zoho default |

With **Shopware source ID**, the Shopware customer's ID is written to the identity field and used to find the same contact on later runs. Choose **Email compatibility mode** only when you deliberately want to match on the email address.

### Create a deal for a new order

| Parameter | Value |
| --- | --- |
| Module | Deals |
| Operation | Create |
| Layout | Standard |
| Deal Pipeline | Standard |
| Initial Deal Stage | Qualification |
| Record Automation | Use Zoho default |

## Related resources

- [Zoho CRM API v8 overview](https://www.zoho.com/crm/developer/docs/api/v8/)
- [Zoho CRM: insert records](https://www.zoho.com/crm/developer/docs/api/v8/insert-records.html)
- [Zoho CRM: upsert records](https://www.zoho.com/crm/developer/docs/api/v8/upsert-records.html)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node received data it could not process**: the mapped record does not fit the selected module and layout. Before calling Zoho, the node checks that every mapped field exists on the layout and is writable, and that every required field is present, for example `Last_Name` for leads and contacts or `Deal_Name` for deals. Zoho can also reject the record itself, for example with a duplicate value in a field Zoho keeps unique (Zoho reports this as duplicate data). Fix the field names or values in the **Field Mapping** node.
- **A duplicate value is rejected although the record should be updated**: **Upsert with one duplicate identity** matches only on the **Identity Field**, and is available only for leads, contacts and accounts. In **Shopware source ID** mode, a clash on another unique field such as Email is still rejected; only **Email compatibility mode** matches on the email address. For deals, use **Update Deal by external ID**.
- **Identity Field offers no options**: for leads, contacts and accounts, the field must be a custom field marked unique in Zoho CRM (the standard Account Name field does not qualify). For deals, it must be a custom external ID field. Add such a field to the module in Zoho CRM, then reopen the node.
- **The node configuration is invalid**: Zoho refused the connection's authorisation, for example because the token is no longer valid, the authorising user lost a permission, or the connection lacks a scope (Zoho reports invalid token, no permission or scope mismatch). Reauthorise the connection under **Integrations** with a user who can access the module. The same message appears when a **Zoho Record ID** expression does not resolve to a valid ID.
- **The node failed unexpectedly**: Zoho was busy, rate limited the request or returned a server error during a write that the node does not repeat. **Create** is never retried, and the update and upsert operations are retried only when **Record Automation** is **Suppress all record automation** (for upserts, also only in **Shopware source ID** mode), because a repeated write could run Zoho automations twice. Check in Zoho CRM whether the record was written before running the workflow again.
- **Dropdowns show "Connect Zoho CRM and complete the preceding selections"**: select the Zoho CRM credential on the node first, then the **Module** and **Layout**; pipelines, stages, identity fields and task options are loaded from Zoho in that order.
- **Zoho workflows, assignment rules or emails fired unexpectedly**: **Record Automation** defaults to Zoho's behaviour. Choose **Suppress all record automation** when Nexus writes should not trigger Zoho automations.
