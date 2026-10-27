---
nav:
  title: Node reference
  position: 4
---

# Node reference

Nexus workflows are built from these nodes. Each page lists every parameter exactly as it appears in the workflow builder, how to set up its credentials, what it outputs, worked examples and the messages you see when something goes wrong.

:::info
**How expressions work**
A field written as <code v-pre>{{field}}</code> is looked up in one combined set of data: the trigger data, a few values some nodes pass along such as `shopUrl`, and the output of the node directly before. When names clash, the previous node wins. The `data.` prefix is optional, so <code v-pre>{{data.orderNumber}}</code> and <code v-pre>{{orderNumber}}</code> are the same lookup. Array items are addressed by position, for example <code v-pre>{{data.0.total}}</code>. The only built-in value is <code v-pre>{{$now}}</code>, optionally with `:date`, `:time`, `:datetime`, `:iso` or `:unix`, always in UTC. Apart from the values some nodes deliberately pass along, the output of a node that is not directly before the current node cannot be referenced.
:::

## Trigger nodes

| Node | Type | Status | Description |
| --- | --- | --- | --- |
| [ERPNext Event Trigger](./triggers/erpnext-webhook.md) | `trigger.erpnext_webhook` |  | Trigger a workflow from an ERPNext (Frappe) document event, such as a submitted Sales Order or a stock update |
| [Schedule Trigger](./triggers/schedule.md) | `trigger.schedule` |  | Trigger workflow on a schedule |
| [Shopware Event Trigger](./triggers/shopware-webhook.md) | `trigger.shopware_webhook` |  | Trigger workflow via Shopware event |

## Action nodes

| Node | Type | Status | Description |
| --- | --- | --- | --- |
| [API Request](./actions/api.md) | `action.api` | Experimental | Make API requests to external services |
| [Akeneo API](./actions/akeneo.md) | `action.akeneo-api` |  | Read and write Akeneo PIM products, attributes, families, and categories |
| [Bitly API](./actions/bitly.md) | `action.bitly-api` |  | Create, update, archive, read, and report on Bitly links, QR codes, account metadata, and engagement metrics. |
| [Box API](./actions/box.md) | `action.box-api` |  | Upload files, stamp metadata, create shared links, poll Events, and call Box JSON endpoints |
| [Brevo](./actions/brevo.md) | `action.brevo` |  | Send emails and manage Brevo contacts and lists |
| [Discord Webhooks](./actions/discord-webhooks.md) | `action.discord-webhooks-api` |  | Send rich, receipt-tracked commerce notifications to a Discord incoming webhook. |
| [ERPNext](./actions/erpnext.md) | `action.erpnext-api` |  | Create, update, upsert, read and submit ERPNext (Frappe) documents such as Customers, Items, Sales Orders and Sales Invoices |
| [GitHub Issues](./actions/github-issues.md) | `action.github-issues-api` |  | Read and manage issues, comments, and labels in the repository selected by a GitHub App connection |
| [Google Calendar](./actions/google-calendar.md) | `action.google-calendar` |  | Create, update, delete, and list timed events on a Google Calendar shared with a service account |
| [HubSpot](./actions/hubspot.md) | `action.hubspot-api` |  | Create, update, upsert, read and search HubSpot CRM records such as Contacts, Companies, Deals and Products |
| [Klaviyo API](./actions/klaviyo.md) | `action.klaviyo-api` |  | Read and write Klaviyo profiles, events, catalog records, lists, and subscription jobs through JSON:API. |
| [Mailchimp](./actions/mailchimp.md) | `action.mailchimp-api` |  | Sync audience members, tag contacts, record events, trigger journeys and read campaign reports |
| [MailerSend API](./actions/mailersend.md) | `action.mailersend-api` |  | Send MailerSend transactional messages and read templates, activity, suppressions, messages, webhooks, and bulk status. |
| [Mailjet](./actions/mailjet.md) | `action.mailjet` |  | Send emails and manage Mailjet contacts and lists |
| [Metabase](./actions/metabase.md) | `action.metabase` |  | Run a saved Metabase question and return its result rows |
| [Microsoft Dynamics Business Central](./actions/business-central.md) | `action.business-central-api` | Experimental | Access and modify data in Business Central entities |
| [Notion](./actions/notion.md) | `action.notion` |  | Creates and updates Notion data source entries and pages from workflow data |
| [Odoo](./actions/odoo.md) | `action.odoo-api` |  | Create, update, upsert, read and list Odoo records such as Contacts, Products, Sale Orders and Invoices |
| [OpenQR API](./actions/openqr.md) | `action.openqr-api` |  | Create, update, list, and read OpenQR QR codes, folders, and uploaded files |
| [Oracle NetSuite API](./actions/netsuite.md) | `action.netsuite-api` | Experimental | Execute SuiteTalk REST record and SuiteQL operations in Oracle NetSuite |
| [Pimcore API](./actions/pimcore.md) | `action.pimcore-api` | Experimental | Read and write Pimcore Datahub GraphQL objects and assets |
| [Postmark API](./actions/postmark.md) | `action.postmark-api` |  | Send transactional Postmark emails and read delivery-reconciliation data. |
| [Pushover](./actions/pushover.md) | `action.pushover-api` |  | Send operational push notifications through Pushover |
| [QRCode Monkey API](./actions/qrcode-monkey.md) | `action.qrcode-monkey-api` |  | Generate branded static QR assets or upload logos with QRCode Monkey. |
| [Resend Email](./actions/resend-email.md) | `action.resend-email` |  | Send a transactional email via Resend |
| [Send Shopware Email](./actions/shopware-email.md) | `action.shopware-email` |  | Send email from your Shopware store |
| [Send Slack Message](./actions/slack-message.md) | `action.slack-message` |  | Send a message to a Slack channel |
| [SendGrid API](./actions/sendgrid.md) | `action.sendgrid-api` |  | Send transactional email, upsert Marketing Contacts, read activity, and manage suppression-safe SendGrid workflows. |
| [Shopware API Call](./actions/shopware-api-call.md) | `action.shopware-api-call` |  | Call a Shopware API |
| [Telegram Bot](./actions/telegram-bot.md) | `action.telegram-bot-api` |  | Send operational Telegram Bot messages to a chat, group, channel, or topic |
| [Todoist API](./actions/todoist.md) | `action.todoist-api` |  | Create, update, sync, and read Todoist operational tasks and comments |
| [Trello API](./actions/trello.md) | `action.trello-api` |  | Read Trello boards, lists, and cards, and create, update, or comment on cards |
| [Zoho CRM](./actions/zoho-crm.md) | `action.zoho-crm-api` | Experimental | Create and update Leads, Contacts, Accounts, Deals, Tasks, and Notes with bounded Zoho CRM operations |
| [goQR / QR Server](./actions/goqr.md) | `action.goqr-api` |  | Generate static QR images or decode QR images through the credentialless goQR API. |
| [ntfy API](./actions/ntfy.md) | `action.ntfy-api` |  | Publish notifications to an ntfy topic |

## Transform nodes

| Node | Type | Status | Description |
| --- | --- | --- | --- |
| [Field Mapping](./transforms/map.md) | `transform.map` |  | Map and transform fields between source and target schemas |

## Condition nodes

| Node | Type | Status | Description |
| --- | --- | --- | --- |
| [If](./conditions/if.md) | `condition.if` |  | Route items based on conditions |
| [Switch](./conditions/switch.md) | `condition.switch` |  | Route data based on multiple conditions |

## Control nodes

| Node | Type | Status | Description |
| --- | --- | --- | --- |
| [Delay](./control/delay.md) | `control.delay` |  | Add a delay before continuing |

## Output nodes

| Node | Type | Status | Description |
| --- | --- | --- | --- |
| [DDownload](./outputs/ddownload.md) | `output.ddownload` |  | Upload files and share hosted download links, list files, and create folders on DDownload |
| [Dropbox](./outputs/dropbox.md) | `output.dropbox` |  | Upload files, create folders, list folders, and create shared links in Dropbox |
| [Gofile](./outputs/gofile.md) | `output.gofile` |  | Upload files to Gofile, delete them, set sharing attributes, and create folders |
| [Google Drive](./outputs/google-drive.md) | `output.google-drive` |  | Upload files, create folders, list files, and create sharing links in a Google Drive shared drive |
| [Google Sheets](./outputs/google-sheets.md) | `output.google-sheets` |  | Writes, updates and reads rows in one tab of a Google spreadsheet, and creates tabs, from workflow data |
| [Microsoft OneDrive](./outputs/onedrive.md) | `output.onedrive` |  | Upload files, create folders, share links, and list folders in OneDrive or SharePoint |
| [S3 Storage](./outputs/s3.md) | `output.s3` |  | Store data to S3 or S3-compatible storage |
| [SFTP](./outputs/sftp.md) | `output.sftp` |  | List, read, write, move, and delete files on an SFTP server, and stream large delimited files as records |
| [Smartsheet](./outputs/smartsheet.md) | `output.smartsheet` |  | Creates, updates, and reads Smartsheet rows, and attaches links to them, from workflow data |
