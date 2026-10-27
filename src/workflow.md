---
nav:
    title: Workflow Builder
    position: 3
---

# Workflow Builder

This page covers the fundamentals of building workflows in Shopware Nexus:

- workflow structure and states
- available node types
- expression syntax for mapping and templates

## Workflow Concepts

A workflow is built from nodes connected on a canvas.

Typical structure:

1. **Trigger** (starts the workflow)
2. **Actions** (do something, e.g. API calls, ERP writes, notifications)
3. **Transforms** (shape/filter data)
4. **Conditions** (branch logic)
5. **Outputs** (store results)

## Workflow builder interface

| Element            | Description                                         |
| ------------------ | --------------------------------------------------- |
| Canvas             | Visual workspace                                    |
| Node Palette       | Available nodes                                     |
| Node Configuration | Params, Credentials, Notes, Debug                   |
| Config Templates   | Save and reuse node configurations across workflows |
| Toolbar            | Save, Publish, Run, Undeploy, Import/Export         |
| Execution Tab      | Run history and metrics                             |

A workflow can also be run manually with an optional input payload, and
workflows can be exported to or imported from JSON for backup and sharing.

## Workflow states

| State       | Description                    | Available Actions |
| ----------- | ------------------------------ | ----------------- |
| Draft       | Editing                        | Save, Publish     |
| Published   | Built and ready                | Execute           |
| Deploying   | Creating deployment            | -                 |
| Active      | Running                        | Undeploy          |
| Inactive    | Deployed but stopped           | Execute, Delete   |
| Undeploying | Removing deployment            | -                 |
| Failed      | Deployment or execution failed | Retry, Delete     |

Workflows move through these states from creation to active execution.

### Execution Metrics

- Status (Success / Failed / Running)
- Execution duration
- Messages processed per node
- Error counts and latency
- Node timeline showing each node's step, label, status, and runtime
- Per-node payload inspection as formatted JSON

## Current Monitoring Limitations

- Manual refresh required

## Versioning

Each time you publish a workflow, Nexus stores an immutable version in its history. From the **Versions** panel you can:

- Review every published version with its author and publish time
- Compare two versions to see node, connector, and setting changes
- Restore an earlier version, which replaces the current draft (existing published versions are untouched)

A workflow must be deactivated before you can restore a version.

## Node types

A workflow combines trigger, action, transform, condition, control and output nodes. The [node reference](./nodes/index.md) documents every node: its parameters, credentials, output, examples and the messages you see when something goes wrong.

## Expression syntax

Expressions use <code v-pre><code v-pre>{{ }}</code></code> syntax in node fields that support them. Type `@` in such a field to pick a field from the trigger or the previous node.

An expression is looked up in one combined set of data:

- the trigger data, for example the Shopware order that started the workflow
- a few values some nodes pass along on purpose, such as `shopUrl`
- the output of the node directly before the current one

When names clash, the previous node wins. The `data.` prefix is optional, so <code v-pre><code v-pre>{{data.orderNumber}}</code></code> and <code v-pre><code v-pre>{{orderNumber}}</code></code> are the same lookup. Array items are addressed by position. The output of a node that is not directly before the current node cannot be referenced, so place the node whose output you need directly before the node that uses it, or copy the values forward with a **Field Mapping** node.

### Examples

```text
{{orderNumber}}
{{orderCustomer.email}}
{{data.0.total}}
{{orderCustomer.firstName}} {{orderCustomer.lastName}}
```

### Built-in values

<code v-pre><code v-pre>{{$now}}</code></code> inserts the time the step ran, always in UTC. Add a format for file names and timestamps: `:date`, `:time`, `:datetime`, `:iso` or `:unix`, for example <code v-pre>stock-{{$now:date}}.csv</code>.

### Common usage

- **Notifications:** build readable Slack, email or chat messages from order data
- **Mapping data:** pass values from the trigger data into action parameters
- **Branching:** drive `If` and `Switch` conditions with values from the data

Refer to user docs for [common use cases](https://docs.shopware.com/en/shopware-6-en/shopware-services/shopware-nexus?category=shopware-6-en/insider-previews#common-use-cases)
