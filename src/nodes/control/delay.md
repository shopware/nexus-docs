---
nav:
  title: "Delay"
  position: 1
---

# Delay

Use the Delay node to pause a workflow before the next step. It is useful when a system needs time to finish processing, when you want to check whether something has happened within a period, or when you need to slow down bursts of calls to a rate-limited service.

Set the **Duration (ms)** between 100 milliseconds and 3,600,000 milliseconds (one hour). The incoming data is passed through unchanged after the pause.

:::info
**Node type:** `control.delay` · **Category:** Control
:::

## Node parameters

Configure this node with these parameters.

### Duration (ms)

Delay duration in milliseconds

- Type: Number
- Required
- Default: `100`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Data to pass through after delay |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `any` | Pass-through data after delay |

The node forwards the incoming data unchanged once the delay has elapsed.

## Examples

### Escalate orders that are not processed within 30 minutes

Connect a **Shopware Event Trigger** listening for **Order placed** to this node:

| Parameter | Value |
| --- | --- |
| Duration (ms) | `1800000` |

After the delay, add a **Shopware API Call** with **Entity Type** Order and **Operation** Get one to reload the order, then an **If** node that checks whether the order state is still open. Connect the **True** port to a **Send Slack Message** node.

### Space out notifications

| Parameter | Value |
| --- | --- |
| Duration (ms) | `1500` |

Place the node in front of a **Send Slack Message** node when the workflow can fire in bursts.

## Common issues

- **Duration is rejected**: the value must be a whole number of milliseconds between 100 and 3,600,000. For longer waits, use a **Schedule Trigger** that runs later and reads the data it needs.
- **The data after the delay is stale**: the node passes through the data it received, not a fresh copy. Reload the record with a **Shopware API Call** or another read step after the delay.
- **The execution shows as running for a long time**: this is expected while the delay is in progress; the delay counts towards the execution duration.
