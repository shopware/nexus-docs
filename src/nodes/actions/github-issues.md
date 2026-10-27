---
nav:
  title: "GitHub Issues"
  position: 8
---

# GitHub Issues

Use the GitHub Issues node to create, read and update issues in one GitHub repository from a workflow. It is a good fit for turning operational events in Shopware into work for a development or operations team: open an issue when an order needs manual review, when a product fails a catalogue check, or when a sync with another system fails, and add comments or labels as the situation changes.

Pick an **Operation**. The repository is fixed by the saved GitHub Issues connection, so the node never asks for an owner or repository name. Operations that work on an existing issue take an **Issue Number**: pick a recent issue from the dropdown, type an issue or pull request number (with or without `#`), or use an expression that resolves to the number; list operations take a page size, a maximum item count and, for issues, a state and label filter.

:::info
**Node type:** `action.github-issues-api` · **Category:** Action
:::

## Credentials

:::info
This node uses a **github-issues** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

Nexus connects to GitHub through a GitHub App. You do not paste a personal access token or a private key into Nexus.

1. Ask your GitHub administrator to install the Nexus GitHub App for the repositories Nexus may use. The app needs **Metadata: read-only** and **Issues: read and write** repository permissions.
2. In Nexus, open **Integrations** and select **GitHub Issues**.
3. Authorise the app in GitHub when prompted. Nexus uses this authorisation only to discover the installations and repositories your GitHub user can see.
4. In **Finish connecting GitHub**, choose the **GitHub account or organisation** and the **Repository**, give the connection a name and save it. Nexus does not store your GitHub user token.

One saved connection targets one repository. Create another connection to work with a different repository. Reauthorising a connection lets you pick a repository again without changing the workflows that use it.

## Operations

- **Get repository** `getRepository`
- **List issues** `listIssues`
- **Get issue** `getIssue`
- **Create issue** `createIssue`
- **Update issue** `updateIssue`
- **Add comment** `addComment`
- **List comments** `listComments`
- **Add labels** `addLabels`
- **Replace labels** `setLabels`
- **List issue labels** `listIssueLabels`
- **List repository labels** `listRepositoryLabels`

## Node parameters

Configure this node with these parameters.

### Operation

GitHub Issues operation to execute

The saved connection fixes the GitHub App installation and repository. The node cannot override either target.

- Type: Dropdown
- Required
- Default: `createIssue`

Options:

- **Get repository** `getRepository`
- **List issues** `listIssues`
- **Get issue** `getIssue`
- **Create issue** `createIssue`
- **Update issue** `updateIssue`
- **Add comment** `addComment`
- **List comments** `listComments`
- **Add labels** `addLabels`
- **Replace labels** `setLabels`
- **List issue labels** `listIssueLabels`
- **List repository labels** `listRepositoryLabels`

### Issue Number

Select a recent issue or enter an issue or pull request number, with or without #, from the connected repository.

- Type: Dropdown
- Required
- Options are loaded from your connected account
- Shown when **Operation** is one of **Get issue**, **Update issue**, **Add comment**, **List comments**, **Add labels**, **Replace labels**, **List issue labels**

### Title

Issue title. Keep customer data out of titles when the selected repository is public.

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create issue**

### Body

Optional Markdown issue body. Everyone with repository access can read it, so include only operationally necessary data.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Create issue**

### New Title

Optional replacement title. Leave empty to keep the current title.

- Type: Text
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update issue**

### New Body

Optional replacement Markdown body. Leave empty to keep the current body.

- Type: Code / template
- Optional
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Update issue**

### Comment

Markdown comment body. GitHub cannot deduplicate comments, so failed comment writes are not automatically retried.

- Type: Code / template
- Required
- Supports expressions (<code v-pre>{{ }}</code>)
- Shown when **Operation** is **Add comment**

### Issue State

Filter listed issues by state

- Type: Dropdown
- Optional
- Default: `open`
- Shown when **Operation** is **List issues**

Options:

- **Open** `open`
- **Closed** `closed`
- **All** `all`

### New State

Optional issue state change

- Type: Dropdown
- Optional
- Shown when **Operation** is **Update issue**

Options:

- **Open** `open`
- **Closed** `closed`

### State Reason

Optional reason for closing or reopening the issue. GitHub validates the reason against the selected state.

- Type: Dropdown
- Optional
- Shown when **Operation** is **Update issue**

Options:

- **Completed** `completed`
- **Not planned** `not_planned`
- **Reopened** `reopened`

### Labels

Optional labels used when creating or filtering issues.

- Type: Multi-select
- Optional
- Options are loaded from your connected account
- Shown when **Operation** is one of **Create issue**, **List issues**

### Labels

Optional labels to add, replace, or update on the issue.

- Type: Multi-select
- Optional
- Options are loaded from your connected account
- Shown when **Operation** is one of **Update issue**, **Add labels**, **Replace labels**

### Page Size

Number of records requested from GitHub per page

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is one of **List issues**, **List comments**, **List issue labels**, **List repository labels**

### Maximum Items

Maximum number of records returned across all pages. Test mode may lower this further.

- Type: Number
- Optional
- Default: `100`
- Shown when **Operation** is one of **List issues**, **List comments**, **List issue labels**, **List repository labels**

### Include Pull Requests

GitHub's issue list can also contain pull requests. Leave disabled for operational issue workflows.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **List issues**

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Shopware order, product, incident, or mapped issue context used by field templates |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | Normalised GitHub repository, issue, comment, label, or list result |

The output object contains a normalised version of GitHub's response for the operation: the repository, the issue, the comment, or the labels affected. List operations return the collected records across the requested pages.

## Examples

### Open an issue when an order needs manual review

Connect a **Shopware Event Trigger** listening for **Order placed**, add an **If** node with your review condition, and configure this node on the true branch:

| Parameter | Value |
| --- | --- |
| Operation | Create issue |
| Title | <code v-pre>Manual review: order {{data.orderNumber}}</code> |
| Body | <code v-pre>Order {{data.orderNumber}} for {{data.amountTotal}} EUR needs a manual check before fulfilment.</code> |
| Labels | `order-review` |

Keep customer names, addresses and payment details out of the title and body if the repository is public or widely shared.

### List open issues for a label

| Parameter | Value |
| --- | --- |
| Operation | List issues |
| Issue State | Open |
| Labels | `order-review` |
| Page Size | `100` |
| Maximum Items | `200` |
| Include Pull Requests | off |

## Related resources

- [GitHub REST API: Issues](https://docs.github.com/en/rest/issues/issues)
- [GitHub REST API: Issue comments](https://docs.github.com/en/rest/issues/comments)
- [GitHub REST API: Labels](https://docs.github.com/en/rest/issues/labels)
- [GitHub Apps: Installation authentication](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/generating-an-installation-access-token-for-a-github-app)
- [GitHub: Installing a GitHub App from a third party](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party)
- [GitHub: Rate limits for the REST API](https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api)
- [GitHub: Basic writing and formatting syntax](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **The node configuration is invalid**: GitHub refused the request because the GitHub App may not act on the repository, for example because the app lacks the Issues read and write permission or an organisation policy blocks it (GitHub often reports this as a resource not accessible by integration). Grant the permission or approve the app in GitHub. The same message appears when **State Reason** does not fit **New State**: a reason can only be sent with **Closed**, and only **Completed** or **Not planned** are accepted.
- **The requested resource was not found**: the issue number does not exist in the connected repository, or the installation can no longer see the repository. Check the number in GitHub and the installation's repository access.
- **The node received data it could not process**: GitHub rejected the request content, for example a value that fails GitHub's validation, or the repository has issues turned off. The same message appears when an expression in **Issue Number**, **Title**, **Body**, **Comment** or **Labels** cannot be resolved, or **Issue Number** does not resolve to a plain positive number.
- **The connected service is rate limiting requests**: GitHub limits requests per app installation. Reads, **Update issue**, **Add labels** and **Replace labels** wait and try again; **Create issue** and **Add comment** are not retried. GitHub reports some limits, such as secondary rate limits, as a refusal rather than a rate-limit response; those show as **Permission denied by the connected service** and clear by themselves.
- **Create issue or Add comment failed but the issue or comment exists**: GitHub offers no way to deduplicate these writes, so they are never retried after an unclear failure such as a timeout. Check the repository before re-running the workflow.
- **The repository is missing when you connect, or a working workflow starts failing**: the GitHub App is not installed for that repository, an organisation owner still has to approve it, or the repository was later removed from the installation. Install or approve the app, or add the repository to the installation's repository access in GitHub, then connect or reauthorise again.
- **List issues returns pull requests**: GitHub's issue list includes pull requests. Leave **Include Pull Requests** off for operational workflows.
