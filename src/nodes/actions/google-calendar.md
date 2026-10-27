---
nav:
  title: "Google Calendar"
  position: 9
---

# Google Calendar

Use the Google Calendar node to create, update, delete and list timed events on a Google Calendar from a workflow. Typical uses are a delivery or installation appointment for an order, a fulfilment slot for the warehouse team, or a follow-up reminder a week after a purchase, all placed on a calendar the team already watches.

Pick an **Operation**. **Create or update event** and **Delete event** address the event through an **Event Key** you choose, for example <code v-pre>delivery-{{data.id}}</code>; the same key always means the same event, so a repeated run updates rather than duplicates. **Create event** makes a new event on every run. **List events** reads a bounded time window and by default returns only events this node created. Events are timed and one-off: all-day and recurring events, attendees and invitation emails are not supported.

:::info
**Node type:** `action.google-calendar` · **Category:** Action
:::

## Credentials

:::info
This node uses a **google-calendar** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

The credential is a Google service account plus one calendar shared with it.

1. In Google Cloud, create a service account in your own project, enable the Google Calendar API for that project and download the service account's JSON key file.
2. In Google Calendar, open the settings of the calendar you want to use, share it with the service account's email address and grant **Make changes to events**. A service account owns no calendar you would use, so this sharing step is required.
3. In Nexus, open **Integrations** and select **Google Calendar**.
4. Paste the whole key file into **Service account key (JSON)**, enter the **Calendar ID** from the calendar's settings under **Integrate calendar** (for example `abc123@group.calendar.google.com`), and optionally a **Calendar name**.
5. Use **Test connection** to verify the key and the share.

Do not use `primary` or a calendar URL as the ID; both are rejected. One credential targets one calendar, so create a credential per calendar, reusing the same key file. Test connection only proves that the calendar can be read; a missing **Make changes to events** role shows up as a permission error on the first write.

## Operations

- **Create or update event** `upsertEvent`
- **Create event** `createEvent`
- **Delete event** `deleteEvent`
- **List events** `listEvents`

## Node parameters

Configure this node with these parameters.

### Operation

Google Calendar operation to execute

- Type: Dropdown
- Required
- Default: `upsertEvent`

Options:

- **Create or update event** `upsertEvent`
- **Create event** `createEvent`
- **Delete event** `deleteEvent`
- **List events** `listEvents`

### Event Key

Stable key that identifies the event across workflow runs, for example fulfilment-<code v-pre>{{orderId}}</code>. The same key always addresses the same event on the credential's calendar, which is what lets a repeated run update instead of duplicating. Supports <code v-pre>{{field}}</code> templating. Non-printable characters are rejected.

- Type: Text
- Required
- Placeholder: <code v-pre>fulfilment-{{orderId}}</code>
- Shown when **Operation** is one of **Create or update event**, **Delete event**

### Summary

Event title shown in Google Calendar. Supports <code v-pre>{{field}}</code> templating. Carriage returns, line feeds, and non-printable characters are rejected.

- Type: Text
- Required
- Placeholder: <code v-pre>Fulfil order {{orderNumber}}</code>
- Shown when **Operation** is one of **Create or update event**, **Create event**

### Start

Event start as an RFC3339 timestamp that includes its time zone, for example 2026-08-24T10:00:00Z or 2026-08-24T10:00:00+02:00. A timestamp without a zone is rejected rather than guessed, because the guess would be wrong by exactly the amount that moves the appointment. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Placeholder: <code v-pre>{{startDateTime}}</code>
- Shown when **Operation** is one of **Create or update event**, **Create event**

### Start Offset (minutes)

Minutes added to Start, so a task can be scheduled relative to real trigger data. 60 puts a fulfilment task an hour after the order, and 10080 puts a follow-up a week after it. Negative values move the event earlier.

- Type: Number
- Optional
- Default: `0`
- Shown when **Operation** is one of **Create or update event**, **Create event**

### End Mode

How the event end is derived. Duration adds a length to the start; Exact end takes a second timestamp.

- Type: Dropdown
- Optional
- Default: `duration`
- Shown when **Operation** is one of **Create or update event**, **Create event**

Options:

- **Duration** `duration`
- **Exact end** `dateTime`

### Duration (minutes)

Event length in minutes, up to one week. Used when End Mode is Duration.

- Type: Number
- Optional
- Default: `30`
- Shown when **Operation** is one of **Create or update event**, **Create event** and **End Mode** is **Duration**

### End

Event end as an RFC3339 timestamp that includes its time zone, and later than the start. Used when End Mode is Exact end. Start Offset shifts both timestamps equally, so the configured length is preserved. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Shown when **Operation** is one of **Create or update event**, **Create event** and **End Mode** is **Exact end**

### Description

Event description. Line breaks are allowed. Anything written here is visible to everyone who can read the calendar, so keep it to what staff need. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Optional
- Shown when **Operation** is one of **Create or update event**, **Create event**

### Location

Free-text location shown on the event. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Optional
- Shown when **Operation** is one of **Create or update event**, **Create event**

### From

Start of the window to read, as an RFC3339 timestamp with its time zone. Google treats this as an exclusive lower bound on an event's END, so an event still running at this moment is included.

- Type: Text
- Required
- Placeholder: `2026-08-01T00:00:00Z`
- Shown when **Operation** is **List events**

### To

End of the window to read, as an RFC3339 timestamp with its time zone, later than From and at most 366 days after it. Google treats this as an exclusive upper bound on an event's START.

- Type: Text
- Required
- Placeholder: `2026-08-31T00:00:00Z`
- Shown when **Operation** is **List events**

### Max Items

Maximum number of events to return across paged requests (capped at 1000). A test run lowers this further.

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is **List events**

### Only Events Created Here

Read only events this connector created. Turning it off returns every event in the window, including people's own meetings, so leave it on unless the workflow genuinely needs the whole calendar.

- Type: Toggle
- Optional
- Default: `true`
- Shown when **Operation** is **List events**

### Include Details

Add description, location, and the Google Calendar link to each returned event. Attendees are never returned.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **List events**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for Google Calendar event key, summary, and time templating |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Google Calendar operation result |

Write operations return `eventId`, `action` (`created`, `updated` or `unchanged`), `status`, `etag`, `start`, `end`, `created`, `updated` and `htmlLink`. **Delete event** returns `deleted` and `alreadyAbsent`. **List events** returns `items`, `total` and `hasMore`; each item includes description, location and the calendar link only when **Include Details** is on.

## Examples

### Put a delivery appointment on the fulfilment calendar

Connect a **Shopware Event Trigger** listening for **Order placed** to this node and configure:

| Parameter | Value |
| --- | --- |
| Operation | Create or update event |
| Event Key | <code v-pre>delivery-{{data.id}}</code> |
| Summary | <code v-pre>Deliver order {{data.orderNumber}}</code> |
| Start | <code v-pre>{{data.orderDateTime}}</code> |
| Start Offset (minutes) | `2880` |
| End Mode | Duration |
| Duration (minutes) | `60` |
| Description | <code v-pre>Order {{data.orderNumber}}, {{data.amountTotal}} EUR.</code> |

The offset places the appointment two days after the order was placed. If the workflow runs again for the same order, the event is updated in place.

### Remove the appointment when the order is cancelled

Use a second workflow triggered by the cancellation event, with the same credential and the same key:

| Parameter | Value |
| --- | --- |
| Operation | Delete event |
| Event Key | <code v-pre>delivery-{{data.id}}</code> |

## Related resources

- [Google Calendar API: Events resource](https://developers.google.com/workspace/calendar/api/v3/reference/events)
- [Google Calendar API: create events](https://developers.google.com/workspace/calendar/api/guides/create-events)
- [Google Calendar API: handle errors](https://developers.google.com/workspace/calendar/api/guides/errors)
- [Google Cloud: use a service account with OAuth 2.0](https://developers.google.com/identity/protocols/oauth2/service-account)
- [Google Calendar Help: Share your calendar with someone](https://support.google.com/calendar/answer/37082)
- [Google Cloud: Create and delete service account keys](https://cloud.google.com/iam/docs/keys-create-delete)
- [Google Calendar API: Manage quotas](https://developers.google.com/workspace/calendar/api/guides/quota)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node received data it could not process**: **Start**, **End**, **From** or **To** is not an RFC 3339 timestamp with an explicit time zone, such as `2026-09-30T10:00:00Z` or `2026-09-30T10:00:00+02:00`. A value without a zone is refused rather than guessed. The same message appears when an expression cannot be resolved, or when **Summary**, **Description** or **Location** is too long or contains control characters.
- **Permission denied by the connected service**: the calendar was shared with the service account with a read-only role, so **Test connection** passed but the first write fails. Open the calendar's sharing settings and grant **Make changes to events**. The same message appears when another credential for the same calendar created the event, because Google only lets the service account that created an event change it, and when the Google Calendar API is not enabled in the key's Google Cloud project.
- **Authentication failed for the connected service**: Google rejected the service account key, for example because it was deleted, disabled or rotated, or the server clock is out of sync. Create a new key and paste it into the credential under **Integrations**.
- **The requested resource was not found**: the **Calendar ID** is wrong, or the calendar is no longer shared with the service account.
- **The node configuration is invalid**: for **List events**, **To** must be later than **From** and at most 366 days after it. The message also appears when Google reports that a calendar usage limit for the account was reached; review Google's Calendar usage limits before running the workflow again.
- **The connected service is rate limiting requests**: Google throttles rapid writes to one calendar. Every operation is retried automatically, and the throttling clears by itself; stagger scheduled workflows that write many events at once.
- **An event is not updated or deleted**: the node only ever changes events it created itself with the same **Event Key** on the same calendar. It never touches meetings people added manually, even when the title matches. A **Delete event** run with a credential for a different calendar reports `alreadyAbsent` while the original event stays, so the cancellation workflow must use the same Google Calendar credential as the create.
