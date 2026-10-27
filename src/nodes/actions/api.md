---
nav:
  title: "API Request"
  position: 1
---

# API Request

Use the API Request node to call any HTTP API from a workflow. It is the node to reach for when a service has no native Nexus connector: a carrier's tracking API, your own middleware, a tax or address validation service, or a webhook endpoint of another automation tool.

Choose a **Method** (GET, POST, PUT, PATCH, DELETE) and enter the **URL**; type `@` to insert fields from the trigger or the previous node, or use expressions such as <code v-pre>{{orderNumber}}</code>. Add **Query parameters** and **Headers** as key-value lists, and for POST, PUT and PATCH pick a **Body type** (JSON, Raw or Form URL encoded) and enter the **Body**. Attach a saved HTTP credential when the API needs authentication. Paste a **Response sample** so the next node gets autocomplete for the response fields. The URL must be publicly reachable: Nexus refuses `localhost`, private network addresses and cloud metadata hosts.

:::info
**Node type:** `action.api` · **Category:** Action
:::

:::warning
**Experimental**
This node is experimental and its behaviour may change.
:::

## Credentials

:::info
A **http** credential is optional for this node. The steps below explain when you need one.
:::

Authentication is optional. To call a protected API, create a credential under **Integrations → Custom integrations → Add integration** and select it in the node's **Credentials** tab. Two credential types cover most APIs:

1. **Static token / API key**: enter the base **URL** of the API, then either an **Authorization header** with a **Scheme** (Bearer, APIKey, Token, a custom scheme or no prefix) and the **Token**, or one or more **Static headers** (for example `X-API-Key`) or **Query parameters** (for example `api_key`). Add at least one of the three.
2. **Basic auth**: enter the **URL**, **Username** and **Password**.

Optionally set a **Test URL** that requires authentication and use **Test connection**. If the test URL is not protected, the test always passes and does not prove the credential works. The credential's values are added to every request the node sends, so you do not repeat them in the node's **Headers**.

## Node parameters

Configure this node with these parameters.

### Method

Request method

- Type: Dropdown
- Required
- Default: `GET`

Options:

- **GET** `GET`
- **POST** `POST`
- **PUT** `PUT`
- **DELETE** `DELETE`
- **PATCH** `PATCH`

### URL

Request URL. Use @ to insert fields from previous nodes. 1

- Type: Text
- Required
- Supports expressions (<code v-pre>{{ }}</code>)

### Query parameters

Optional request query parameters

- Type: Key-value list
- Optional

### Headers

Optional HTTP headers

- Type: Key-value list
- Optional
- Default: `{"Accept": "application/json"}`

### Body type

How to build the request body

- Type: Dropdown
- Required
- Default: `none`
- Shown when **Method** is one of **POST**, **PUT**, **PATCH**

Options:

- **None** `none`
- **JSON** `json`
- **Raw** `raw`
- **Form URL encoded** `form-urlencoded`

### Body

Request body. Use JSON for JSON and form-url-encoded body types.

- Type: Code / template
- Optional
- Shown when **Method** is one of **POST**, **PUT**, **PATCH** and **Body type** is one of **JSON**, **Raw**, **Form URL encoded**

### Timeout (ms)

Request timeout in milliseconds

- Type: Number
- Optional
- Default: `10000`

### Response sample

Optional JSON sample of the API response. Used only for downstream autocomplete. Autocomplete suggestions are limited to the first 100 fields and 8 nested levels; the full JSON sample can still be larger and deeper.

- Type: Code / template
- Optional

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Response | `object` | HTTP response |

The output object has four fields: `status` (the HTTP status code), `headers`, `body` (the raw response text) and `json` (the parsed body when the response is valid JSON, otherwise empty). The next node reads a field of a JSON response as <code v-pre>{{json.&lt;field&gt;}}</code>, for example <code v-pre>{{json.id}}</code> or <code v-pre>{{json.items.0.name}}</code>. Provide a **Response sample** in this node so the field picker in the next node can suggest these paths. Only responses with a 2xx status reach the output; any other status fails the step.

## Examples

### Create a shipment with a carrier API

Connect a **Shopware Event Trigger** listening for **Delivery status changed to shipped** (ID `state_enter.order_delivery.state.shipped`) to this node:

| Parameter | Value |
| --- | --- |
| Method | POST |
| URL | `https://api.example-carrier.com/v1/shipments` |
| Headers | `Content-Type`: `application/json` |
| Body type | JSON |
| Body | <code v-pre>{"reference": "{{orderNumber}}", "recipient": "{{orderCustomer.email}}"}</code> |
| Timeout (ms) | `15000` |

Select the carrier's **Static token / API key** credential in the **Credentials** tab.

### Look up a customer in your CRM

| Parameter | Value |
| --- | --- |
| Method | GET |
| URL | `https://crm.example.com/api/customers` |
| Query parameters | `email`: <code v-pre>{{email}}</code> |

## Related resources

- [HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [HTTP status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

## Common issues

- **Authentication failed for the connected service**: the API answered that the request is not authenticated. The credential is missing, wrong, or placed where the API does not expect it. Check whether the API wants the token in the Authorization header, a custom header or a query parameter, and test the credential with a protected **Test URL**. If the API instead answers that the caller is not allowed, Nexus shows **Permission denied by the connected service**; check the token's scopes or role in that service.
- **The node configuration is invalid**: the request was never sent. Common causes are a **Body** that is not valid JSON once the expressions are filled in (for example a value containing quotes), a **Form URL encoded** body that is not a JSON object, an empty **URL**, or a URL that points to `localhost`, a private network address or a scheme other than `http` or `https`.
- **The connected service rejected the request**: the API refused the request content, for example a missing required field or a value it does not accept. Compare the **Body** and **Query parameters** with the API's documentation. Unusual refusals, such as an unsupported content type or method, appear as **The node failed unexpectedly**; check that the `Content-Type` header matches the **Body type**.
- **The connected service did not respond in time**: the request took longer than **Timeout (ms)** (default 10 seconds, maximum 60 seconds). Raise the timeout or call a faster endpoint.
- **The connected service is rate limiting requests**: the API throttled the call. The node sends each request once and does not retry, for any method, because a repeated POST could create a record twice. Space out runs with a **Delay** node or reduce how often the workflow fires. Server errors on the API side (**The connected service is currently unavailable**) are not retried either.
- **The next node offers no fields from the response**: add a **Response sample**. Autocomplete covers the first 100 fields and 8 nested levels of the sample; the real response can be larger.
- **The json field is empty although the API returned data**: the response was not valid JSON, or it was larger than 512 KB, which Nexus cuts off before parsing. For responses that are not JSON, read <code v-pre>{{body}}</code> instead; for oversized responses, which are cut off in `body` as well, ask the API for a smaller page.
