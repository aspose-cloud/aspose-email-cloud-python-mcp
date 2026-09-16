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

pip install git+https://github.com/aspose-cloud/aspose-email-cloud-python-mcp.git
```

This installs the `aspose-email-mcp` command into your virtual environment.

### 2. Configure your AI client

Two environment variables are required - both come from your Aspose Cloud dashboard's Applications page:

| Variable | Value |
|---|---|
| `ASPOSE_CLIENT_ID` | Your application's Client ID |
| `ASPOSE_CLIENT_SECRET` | Your application's Client Secret |

Credentials are never passed as a tool parameter - the server resolves them once at launch from
these environment variables, exchanges them for a short-lived OAuth2 token, and caches/refreshes it
transparently.

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
        "ASPOSE_CLIENT_SECRET": "your-client-secret"
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
        "ASPOSE_CLIENT_SECRET": "your-client-secret"
      }
    }
  }
}
```

---

## Available tools

| Tool | What it does | Read-only | Dry-run |
|---|---|---|---|
| `email_get_calendar` | Get calendar file from storage. | yes | - |
| `email_get_calendar_list` | Get iCalendar list from storage folder. | yes | - |
| `email_get_calendar_as_alternate` | Get iCalendar from storage as AlternateView | yes | - |
| `email_get_calendar_as_file` | Converts calendar document from storage to specified format and returns as file. | yes | - |
| `email_get_contact` | Get contact document from storage. | yes | - |
| `email_get_contact_list` | Get contact list from storage folder. | yes | - |
| `email_get_contact_as_file` | Converts contact document from storage to specified format and returns as file | yes | - |
| `email_get_list` | Get email list from storage folder. | yes | - |
| `email_get_as_file` | Converts email document from storage to specified format and returns as file | yes | - |
| `email_extract_properties` | Extract structured metadata from a local email file | no | yes |
| `email_convert_file` | Convert a local email file to another format | yes | - |
| `email_get_mapi_calendar` | Get MAPI calendar document. | yes | - |
| `email_get_mapi_contact` | Get MAPI contact document. | yes | - |
| `email_get_mapi_message` | Get MAPI message document. | yes | - |
| `email_get_disposable_is_disposable` | Check email address is disposable | yes | - |
| `email_get_config_discover` | Discover email accounts by email address. Does not validate discovered accounts. | yes | - |
| `email_get_client_account` | Get email client account from storage. | yes | - |
| `email_get_client_account_multi` | Get email client multi account file (*.multi.account). Will respond error if file extension is not ".multi.account". | yes | - |
| `email_delete_client_folder` | Delete a folder in email account | no | yes |
| `email_get_client_folder_list` | Get folders list in email account | yes | - |
| `email_create_client_message` | Send an email specified by model in request. | no | yes |
| `email_get_client_message` | Fetch message from email account | yes | - |
| `email_delete_client_message` | Delete message. | no | yes |
| `email_get_client_message_file` | Fetch message as file from email account | yes | - |
| `email_get_client_message_list` | Get messages from folder, filtered by query | yes | - |
| `email_get_client_thread_list` | Get message threads from folder. All messages are partly fetched (without email body and some other fields). | yes | - |
| `email_get_client_thread_messages` | Get messages from thread by id. All messages are fully fetched. For accounts with CacheFile only cached messages will be returned. | yes | - |
| `email_delete_client_thread` | Delete thread by id. All messages from thread will also be deleted. | no | yes |
| `email_get_ai_name_parse` | Parse name to components. | yes | - |
| `email_get_ai_name_genderize` | Detect person's gender from name string. | yes | - |
| `email_get_ai_name_complete` | The call proposes k most probable names for given starting characters. | yes | - |
| `email_get_ai_name_format` | Formats a person's name in correct case and name order using options for formatting instructions. | yes | - |
| `email_get_ai_name_expand` | Expands a person's name into a list of possible alternatives using options for expanding instructions. | yes | - |
| `email_get_ai_name_match` | Compare people's names. Uses options for comparing instructions. | yes | - |
| `email_get_ai_name_parse_email_address` | Parse person's name out of an email address. | yes | - |
| `email_get_storage_file` | Download file | yes | - |
| `email_delete_storage_file` | Delete file | no | yes |
| `email_update_storage_file_copy` | Copy file | no | yes |
| `email_update_storage_file_move` | Move file | no | yes |
| `email_get_storage_folder` | Get all files and folders within a folder | yes | - |
| `email_update_storage_folder` | Create the folder | no | yes |
| `email_delete_storage_folder` | Delete folder | no | yes |
| `email_update_storage_folder_copy` | Copy folder | no | yes |
| `email_update_storage_folder_move` | Move folder | no | yes |
| `email_get_storage_exist` | Check if storage exists | yes | - |
| `email_get_storage_disc` | Get disc usage | yes | - |
| `email_get_storage_version` | Get file versions | yes | - |

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
