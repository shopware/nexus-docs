---
nav:
  title: "SFTP"
  position: 8
---

# SFTP

Use the SFTP node to exchange files with an SFTP server from a workflow. Typical Shopware uses are dropping a nightly product or stock export for a partner, picking up order files a supplier or EDI provider uploads, and archiving files once they have been processed. There is no vendor API behind it: the node moves file content and delimited rows between your workflow and a server you or your partner control.

Pick an **Operation**: **List files**, **Read file**, **Read records**, **Write file**, **Move file** or **Delete file**. Every path is resolved inside the **Root directory** set on the credential when there is one, otherwise it must be absolute. Shape the data before the node with a **Field Mapping** node; the SFTP node itself only reads and writes.

:::info
**Node type:** `output.sftp` · **Category:** Output
:::

## Credentials

:::info
This node uses a **sftp** credential. Create it under **Integrations** in Nexus before you configure the node.
:::

1. In Nexus, open **Integrations** and select **SFTP**.
2. Enter **Host**, **Port** (22 unless your server uses another port) and **Username**.
3. Pick the authentication method: **Password**, or **Private key** with the **Private key (PEM)** and, if the key is encrypted, its **Key passphrase**.
4. Choose **Discover host key**. Nexus connects to the server without sending any credentials and shows the server's **Host key fingerprint** (`SHA256:…`). Compare it with the fingerprint your hosting provider or server administrator gives you, then confirm it. The fingerprint pins the server identity: a server presenting a different key is rejected on every later connection.
5. Optionally set a **Root directory** (for example `/upload`). All paths in workflows are then relative to it.
6. Choose **Test connection** to authenticate against the server and verify the pinned host key.

Plain FTP and FTPS are not supported; the server must speak SFTP (SSH).

## Operations

- **List files** `listFiles`
- **Read file** `readFile`
- **Read records** `readRecords`
- **Write file** `writeFile`
- **Move file** `moveFile`
- **Delete file** `deleteFile`

## Node parameters

Configure this node with these parameters.

### Operation

SFTP operation to execute

Transfer duration depends on file size, server speed, and network conditions, and adds to the overall workflow execution time.

- Type: Dropdown
- Required
- Default: `listFiles`

Options:

- **List files** `listFiles`
- **Read file** `readFile`
- **Read records** `readRecords`
- **Write file** `writeFile`
- **Move file** `moveFile`
- **Delete file** `deleteFile`

### Remote Path

Path to the directory to list, or the file to read, write, move, or delete. When the credential sets a root directory, paths are relative to it ("incoming/orders.csv"); otherwise give an absolute path ("/outbound/orders.csv"). Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Placeholder: `incoming/orders.csv`

### Match Pattern

Glob pattern applied to file names in the listed directory, for example *.csv.

- Type: Text
- Optional
- Default: `*`
- Shown when **Operation** is **List files**

### Max Items

Maximum number of directory entries to return.

- Type: Number
- Optional
- Default: `200`
- Shown when **Operation** is **List files**

### Content Encoding

How file content is encoded in the workflow. Use base64 for binary data; text is for character data only.

- Type: Dropdown
- Optional
- Default: `text`
- Shown when **Operation** is one of **Read file**, **Write file**

Options:

- **Text** `text`
- **Base64** `base64`

### File Encoding

Character set of the remote file when content is handled as text. NDJSON is always UTF-8 (RFC 8259), so this does not apply to it.

- Type: Dropdown
- Optional
- Default: `utf-8`
- Shown when **Operation** is one of **Read file**, **Write file** and **Content Encoding** is **Text** or **Operation** is **Read records** and **Record Format** is one of **CSV**, **TSV**

Options:

- **UTF-8** `utf-8`
- **ISO-8859-1 (Latin-1)** `iso-8859-1`
- **Windows-1252** `windows-1252`

### Record Format

Format of the delimited file to stream as records.

- Type: Dropdown
- Required
- Default: `csv`
- Shown when **Operation** is **Read records**

Options:

- **CSV** `csv`
- **TSV** `tsv`
- **NDJSON** `ndjson`

### Delimiter

Field delimiter character.

- Type: Text
- Optional
- Default: `,`
- Shown when **Operation** is **Read records** and **Record Format** is one of **CSV**, **TSV**

### Has Header Row

Treat the first row as column names and emit records as objects keyed by them.

- Type: Toggle
- Optional
- Default: `true`
- Shown when **Operation** is **Read records** and **Record Format** is one of **CSV**, **TSV**

### Max Records

Maximum records per page. Reading resumes from the saved position on the next run.

- Type: Number
- Optional
- Default: `500`
- Shown when **Operation** is **Read records**

### Content

File content as text or base64 per Content Encoding, up to 1 MiB decoded. Larger delimited files are read with Read records; larger binaries are out of scope for this node. Supports <code v-pre>{{field}}</code> templating.

- Type: Code / template
- Required
- Shown when **Operation** is **Write file**

### Write Mode

Create fails when the file already exists; overwrite replaces it. Writes are staged and renamed atomically either way.

- Type: Dropdown
- Optional
- Default: `create`
- Shown when **Operation** is **Write file**

Options:

- **Create** `create`
- **Overwrite** `overwrite`

### Create Directories

Create missing parent directories of the target path.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is one of **Write file**, **Move file**

### Target Path

Destination path for the server-side move, resolved by the same rule as Remote Path: relative to the credential's root directory when one is set, absolute otherwise. Supports <code v-pre>{{field}}</code> templating.

- Type: Text
- Required
- Placeholder: `archive/orders.csv`
- Shown when **Operation** is **Move file**

### Overwrite Existing

Replace an existing file at the target path instead of failing.

- Type: Toggle
- Optional
- Default: `false`
- Shown when **Operation** is **Move file**

### Ignore Missing

Succeed when the file to delete does not exist.

- Type: Toggle
- Optional
- Default: `true`
- Shown when **Operation** is **Delete file**

### Connect Timeout (seconds)

Timeout for establishing the SSH connection.

- Type: Number
- Optional
- Default: `20`

## Input and output

### Input

| Port | Type | Description |
| --- | --- | --- |
| Input | `any` | Input data for remote path and content templating |

### Output

| Port | Type | Description |
| --- | --- | --- |
| Output | `object` | SFTP operation result |

The output depends on the operation. **List files** returns `items`, one entry per file with its name, path, size and modification time. **Read file** returns the file content as text or base64 as configured. **Read records** returns `records`, one object per row keyed by the header row (or one array per row without a header), plus a flag telling you whether more pages remain. **Write file** and **Move file** return a result object for the operation; **Delete file** reports `deleted: false` when the file was already gone.

## Examples

### Drop a daily stock file for a partner

Start with a **Schedule Trigger**, read the products with a **Shopware API Call** node, build the CSV text into a field called `csv` with a **Field Mapping** node placed directly before this one, and configure:

| Parameter | Value |
| --- | --- |
| Operation | Write file |
| Remote Path | <code v-pre>outbound/stock-{{$now:date}}.csv</code> |
| Content | <code v-pre>{{csv}}</code> |
| Content Encoding | Text |
| File Encoding | UTF-8 |
| Write Mode | Create |
| Create Directories | true |

### Import supplier orders from a large CSV

| Parameter | Value |
| --- | --- |
| Operation | Read records |
| Remote Path | `incoming/orders.csv` |
| Record Format | CSV |
| Delimiter | `;` |
| Has Header Row | true |
| Max Records | `500` |

Each run reads one page of up to **Max Records** rows and remembers where it stopped, so a scheduled workflow drains a large file across runs. Add a **Move file** step to `archive/orders.csv` once the file has been fully read.

## Related resources

- [SFTP protocol (OpenSSH)](https://www.openssh.com/)
- [OpenSSH: ssh-keygen (creating key pairs)](https://man.openbsd.org/ssh-keygen)
- [SFTP protocol draft (IETF)](https://datatracker.ietf.org/doc/html/draft-ietf-secsh-filexfer-02)

## What to do if your operation isn't supported

If this node doesn't support the operation you need, use the [API Request node](../actions/api.md) to call the service's API directly. Add the authentication headers the service expects and use expressions to pass in data from the trigger or the previous node.

## Common issues

- **Host key mismatch**: the server presents a different key than the one pinned on the credential, either because the server was reinstalled or because something sits between Nexus and the server. Confirm the new fingerprint with the server owner, then discover and pin it again under Integrations.
- **Write file fails because the target already exists**: **Write Mode** `Create` refuses to replace a file. Switch to `Overwrite`, or put a date or order number in the file name. The same applies to **Move file** unless **Overwrite Existing** is enabled.
- **File too large**: **Read file** and **Write file** handle up to 1 MiB of decoded content. Read larger delimited files with **Read records**, or hand a large binary on unread with **Move file**.
- **Read records only returns the first page**: one workflow run processes one page. Run the workflow on a schedule so the cursor advances; a manual run delivers the first page only. If the file is replaced on the server (size or modification time change), reading starts again from the top.
- **Read records stops at a malformed row**: a ragged CSV line or invalid NDJSON line fails the read at that row; rows before it are delivered. Ask the partner for a corrected file, which resets the cursor.
- **Path not found or permission denied**: check whether the credential has a **Root directory**. With one set, paths are relative to it (`incoming/orders.csv`); without one they must be absolute (`/outbound/orders.csv`).
- **Listing a huge directory fails**: **List files** reads the whole directory before applying **Match Pattern** and **Max Items**. Ask the partner to use drop subfolders instead of one directory with hundreds of thousands of entries.
