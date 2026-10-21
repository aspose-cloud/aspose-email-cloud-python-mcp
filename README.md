# Aspose.Email Cloud MCP Server

Email, calendar, and contact processing — read/convert messages, calendars, and contacts; manage mail client accounts, folders, and threads; parse and format personal names.

An [MCP](https://modelcontextprotocol.io) server exposing Aspose.Email Cloud's REST API as typed,
agent-callable tools. Also bundles Aspose Storage Cloud's core file operations
(`storage_upload_file`/`storage_download_file`/`storage_list_files`/`storage_delete_file`), so a
client connected to only this server can complete a full upload -> process -> download workflow with
no second server connection.

Handles: MSG, EML, ICS, VCF, PST, MHTML.

---

## Requirements

- Python 3.11 or later
- An [Aspose Cloud](https://dashboard.aspose.cloud/) account (free evaluation tier available) - you'll
  need a **Client ID** and **Client Secret** from your dashboard's Applications page
- An MCP-compatible AI client (Claude Desktop, Claude Code, VS Code, Cursor, Cline, Windsurf, etc.)

---

## Setup

### 1. Create a virtual environment and install

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS / Linux:
source .venv/bin/activate

pip install git+https://github.com/aspose-cloud/aspose-storage-cloud-python-mcp.git@v1.0.5 git+https://github.com/aspose-cloud/aspose-email-cloud-python-mcp.git@v1.3.1
```

This installs the `aspose-email-mcp` command into your virtual environment. The first package is the shared file-tool library (upload, download, list, delete) that every Aspose Cloud MCP server depends on; it is not on PyPI, so it is installed from GitHub in the same command.

### 2. Configure your AI client

Three environment variables are required. The first two come from your Aspose Cloud dashboard's Applications page; the third is a folder you create on your own computer (see [Your files folder](#your-files-folder-aspose_files_path) below):

| Variable | Value |
|---|---|
| `ASPOSE_CLIENT_ID` | Your application's Client ID |
| `ASPOSE_CLIENT_SECRET` | Your application's Client Secret |
| `ASPOSE_FILES_PATH` | Full path of the folder on your computer where your input files are placed and results are saved |

Credentials are never passed as a tool parameter - the server resolves them once at launch from
these environment variables, exchanges them for a short-lived OAuth2 token, and caches/refreshes it
transparently.

#### Your files folder (`ASPOSE_FILES_PATH`)

Create a folder on your computer (for example `C:\Users\you\Documents\aspose-files` on Windows, or
`/home/you/aspose-files` on macOS/Linux) and set `ASPOSE_FILES_PATH` to its full path. It is the one
place this server reads your files from and writes results to:

- **Input:** put the document you want processed in that folder, then ask your assistant to upload it with
  `storage_upload_file`, giving only the bare filename (for example `report.docx`). The upload sends it to
  your Aspose Cloud Storage, where the processing tools read it. Subfolders and full paths are rejected.
- **Output:** `storage_download_file` saves a result from Cloud Storage back into the same folder, and its
  reply contains the full local path of the saved file.
- The file and folder arguments of the other tools name locations in your Aspose Cloud Storage, not your
  computer.

#### Claude Desktop

Config file location:

| Platform | Path |
|---|---|
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |

```json
{
  "mcpServers": {
    "email": {
      "command": "C:\\path\\to\\.venv\\Scripts\\aspose-email-mcp.exe",
      "env": {
        "ASPOSE_CLIENT_ID": "your-client-id",
        "ASPOSE_CLIENT_SECRET": "your-client-secret",
        "ASPOSE_FILES_PATH": "C:\\Users\\you\\Documents\\aspose-files"
      }
    }
  }
}
```

On macOS/Linux, use `/path/to/.venv/bin/aspose-email-mcp` instead. Fully quit and restart Claude Desktop after
editing.

#### VS Code (`.vscode/mcp.json`), Cursor (`~/.cursor/mcp.json`), Cline, Windsurf

Same shape, under a `"servers"` key instead of `"mcpServers"` for VS Code:

```json
{
  "servers": {
    "email": {
      "type": "stdio",
      "command": "/path/to/.venv/bin/aspose-email-mcp",
      "env": {
        "ASPOSE_CLIENT_ID": "your-client-id",
        "ASPOSE_CLIENT_SECRET": "your-client-secret",
        "ASPOSE_FILES_PATH": "/home/you/aspose-files"
      }
    }
  }
}
```

---

## Available tools

| Tool | What it does | Read-only | Dry-run |
|---|---|---|---|
| `email_get_calendar` | Retrieves the specified iCalendar file from cloud storage. | yes | - |
| `email_get_calendar_list` | Retrieves a paginated list of iCalendar files stored in a specified folder. | yes | - |
| `email_get_calendar_as_alternate` | Retrieves an iCalendar file from Aspose Cloud storage and returns its AlternateView representation based on the specified calendar action. | yes | - |
| `email_get_calendar_as_file` | This tool fetches a calendar document from Aspose Cloud storage, converts it to the specified format, and returns the resulting file. | yes | - |
| `email_run_calendar_from_file` | The tool converts an uploaded calendar file into a structured calendar model representation. | no | yes |
| `email_get_contact` | The tool retrieves a contact document from the specified storage location. | yes | - |
| `email_get_contact_list` | Retrieves a list of email contacts from a specified folder and storage using the given contact document format. | yes | - |
| `email_get_contact_as_file` | Converts a contact document stored in Aspose Cloud from one format to another and returns the resulting file as binary data. | yes | - |
| `email_run_contact_from_file` | Converts a contact document file (VCard, WebDav, or Msg) into its Aspose.Email model representation. | no | yes |
| `email_get_list` | The tool retrieves a list of email files stored in a specified folder of Aspose Cloud storage. | yes | - |
| `email_get_as_file` | The tool fetches an email document from Aspose Cloud storage, converts it to a specified format, and returns the result as a downloadable file. | yes | - |
| `email_extract_properties` | This tool converts an email document file into a structured email model representation. | no | yes |
| `email_convert_file` | It converts an uploaded email document from a given source format to a specified target format and returns the resulting file. | yes | - |
| `email_get_mapi_calendar` | This tool retrieves a MAPI calendar file from Aspose Cloud storage. | yes | - |
| `email_get_mapi_contact` | Retrieves a MAPI contact document from Aspose Cloud storage in the format you specify. | yes | - |
| `email_get_mapi_message` | This tool retrieves a specific email document stored as a MAPI message from Aspose Cloud. | yes | - |
| `email_get_disposable_is_disposable` | This tool determines whether a given email address is a disposable (temporary) address. | yes | - |
| `email_get_config_discover` | The tool discovers email server configuration settings for a given email address without validating the discovered accounts. | yes | - |
| `email_save_client_account` | The tool creates or updates an email client account file (*.account) in the specified storage location using the supplied credentials. | no | yes |
| `email_get_client_account` | Retrieves the email client account configuration stored in a specified file on Aspose Cloud storage. | yes | - |
| `email_get_client_account_multi` | Retrieves a multi‑account email client file (must have the “.multi.account” extension) from Aspose Cloud storage. | yes | - |
| `email_create_client_folder` | The tool creates a new mail folder in a specified email account. | no | yes |
| `email_delete_client_folder` | Deletes a specified folder from a user's email account. | no | yes |
| `email_get_client_folder_list` | The tool retrieves the list of folders (or sub‑folders) for a specified email account. | yes | - |
| `email_get_client_message` | Retrieves a single email message from a specified email account (and optional folder) and returns it in the requested representation. | yes | - |
| `email_delete_client_message` | Deletes a specific email message from the user's mailbox. | no | yes |
| `email_get_client_message_file` | Fetches a single email message from a specified account and returns it as a downloadable file in the chosen format. | yes | - |
| `email_append_client_message_file` | Adds an email message from a supplied file into a specified folder of a given email account. | no | yes |
| `email_get_client_message_list` | Retrieves a list of email messages from a specified folder in a given email account, optionally filtered by a query string and returned in a chosen representation. | yes | - |
| `email_get_client_thread_list` | The tool retrieves a list of email message threads from a specified folder in a given email account, returning only partial information for each message (no body or full… | yes | - |
| `email_get_client_thread_messages` | Retrieves all messages that belong to a given email thread, optionally scoped to a specific folder or storage location. | yes | - |
| `email_delete_client_thread` | Deletes an email thread identified by its ID, removing all messages contained in that thread. | no | yes |
| `email_run_ai_bcr_parse_storage` | The tool parses one or more email image files stored in Aspose Cloud storage and creates corresponding vCard files in a specified output folder. | no | yes |
| `email_run_ai_bcr_parse` | The tool extracts contact information from an uploaded image and returns it as structured vCard document models. | no | yes |
| `email_get_ai_name_parse` | Parse a personal name into its constituent components (e.g., given name, middle name, surname, title, etc.) using the Aspose Cloud AiName service. | yes | - |
| `email_get_ai_name_genderize` | This tool invokes the Aspose Cloud AI Name genderize endpoint to infer the most likely gender for a given personal name. | yes | - |
| `email_get_ai_name_complete` | This tool returns up to k suggested full names that best complete the supplied name prefix. | yes | - |
| `email_get_ai_name_format` | Formats a given person’s name into the correct case and order using optional language, region, script, style, and formatting settings, and returns the formatted name. | yes | - |
| `email_get_ai_name_expand` | This tool expands a given personal name into a list of possible alternative spellings or forms based on optional language, location, encoding, script, and style settings. | yes | - |
| `email_get_ai_name_match` | This tool compares two personal names and returns a similarity assessment. | yes | - |
| `email_get_ai_name_parse_email_address` | This tool extracts a person’s name from a given email address using Aspose Cloud’s AiName parsing service. | yes | - |
| `email_get_storage_file` | The tool downloads a file from Aspose Cloud Email storage and returns the file’s binary contents. | yes | - |
| `email_delete_storage_file` | Deletes a file stored in Aspose Cloud at the specified path. | no | yes |
| `email_update_storage_file_copy` | Copies a file from a specified source path in Aspose Cloud storage to a given destination path, optionally using different source or destination storages or a particular… | no | yes |
| `email_update_storage_file_move` | Moves a file from a source location to a destination location within Aspose Cloud storage, optionally specifying different storage names or a file version. | no | yes |
| `email_get_storage_folder` | Retrieves a list of all files and sub‑folders that exist inside a given email storage folder. | yes | - |
| `email_update_storage_folder` | Creates a new folder in the specified Aspose Cloud email storage at the given path. | no | yes |
| `email_delete_storage_folder` | Deletes a specified folder from Aspose Cloud email storage. | no | yes |
| `email_update_storage_folder_copy` | Copies a folder from a source path to a destination path in Aspose Email Cloud storage. | no | yes |
| `email_update_storage_folder_move` | Moves an existing folder from one location to another within Aspose Cloud storage. | no | yes |
| `email_get_storage_exist` | The tool checks whether a specified Aspose Cloud storage container exists. | yes | - |
| `email_get_storage_disc` | The tool returns the current disc usage statistics for Aspose.Email cloud storage. | yes | - |
| `email_get_storage_version` | Retrieves the complete version history for a specified file stored in Aspose Email Cloud. | yes | - |

Every mutating tool marked "yes" under **Dry-run** accepts a `dry_run=true` parameter to preview
the change without applying it.

Tool errors use a fixed taxonomy (bad input / auth failure / server error / rate limited), returned
as structured MCP tool errors - never a silent failure or a raw exception message.

---

## Part of the Aspose Cloud MCP family

One MCP server per Aspose Cloud product, published under [github.com/aspose-cloud](https://github.com/aspose-cloud).
This server depends on [`aspose-storage-core-mcp`](https://github.com/aspose-cloud/aspose-storage-cloud-python-mcp)
for its bundled storage tools — that repo is a shared library, not a standalone server (there's no
real Aspose Cloud API route for storage on its own; every real storage call goes through some
product's own gateway, this one included).

---

## License

MIT (see [`LICENSE`](LICENSE)) — covers only this repository's own MCP wrapper/integration code.
It does **not** cover, and grants no rights to, the Aspose Cloud product or API themselves, which
remain governed entirely by [Aspose's own product and usage terms](https://purchase.aspose.cloud/policies).
A valid Aspose Cloud account and subscription/credentials are required to actually call the API,
regardless of this code's license.
