# Aspose.Email Cloud MCP Server - Agent Guide

Guidance for AI agents connected to this MCP server.

## Core rules

1. **Credentials are never a tool parameter.** They're resolved once at server launch from the
   `ASPOSE_CLIENT_ID`/`ASPOSE_CLIENT_SECRET` environment variables. Never ask the user to pass a
   credential into a tool call, and never accept one if offered.
2. **This server bundles Aspose Storage Cloud's core-4 tools** (`storage_upload_file`,
   `storage_download_file`, `storage_list_files`, `storage_delete_file`) so you can complete a full
   workflow without a second server connection: upload a file, call a `email_*` tool against it
   by filename, then download the result.
3. **One product per server.** This server only understands MSG, EML, ICS, VCF, PST, MHTML. Route a request for
   a different file format to that format's own Aspose Cloud MCP server
   (`aspose-<product>-cloud-python-mcp` under [github.com/aspose-cloud](https://github.com/aspose-cloud))
   rather than attempting it here.
4. **Every mutating tool supports a dry-run.** Pass `dry_run=true` to preview a destructive or
   costly operation before committing to it - use this when the user's intent is ambiguous.
5. **Tool errors are structured**, not raw exceptions (bad input / auth failure / server error /
   rate limited). Surface the real reason to the user rather than retrying blindly.

## Tools at a glance

- `email_get_calendar` - Get calendar file from storage.
- `email_get_calendar_list` - Get iCalendar list from storage folder.
- `email_get_calendar_as_alternate` - Get iCalendar from storage as AlternateView
- `email_get_calendar_as_file` - Converts calendar document from storage to specified format and returns as file.
- `email_get_contact` - Get contact document from storage.
- `email_get_contact_list` - Get contact list from storage folder.
- `email_get_contact_as_file` - Converts contact document from storage to specified format and returns as file
- `email_get_list` - Get email list from storage folder.
- `email_get_as_file` - Converts email document from storage to specified format and returns as file
- `email_extract_properties` - Extract structured metadata from a local email file
- `email_convert_file` - Convert a local email file to another format
- `email_get_mapi_calendar` - Get MAPI calendar document.
- `email_get_mapi_contact` - Get MAPI contact document.
- `email_get_mapi_message` - Get MAPI message document.
- `email_get_disposable_is_disposable` - Check email address is disposable
- `email_get_config_discover` - Discover email accounts by email address. Does not validate discovered accounts.
- `email_get_client_account` - Get email client account from storage.
- `email_get_client_account_multi` - Get email client multi account file (*.multi.account). Will respond error if file extension is not ".multi.account".
- `email_delete_client_folder` - Delete a folder in email account
- `email_get_client_folder_list` - Get folders list in email account
- `email_create_client_message` - Send an email specified by model in request.
- `email_get_client_message` - Fetch message from email account
- `email_delete_client_message` - Delete message.
- `email_get_client_message_file` - Fetch message as file from email account
- `email_get_client_message_list` - Get messages from folder, filtered by query
- `email_get_client_thread_list` - Get message threads from folder. All messages are partly fetched (without email body and some other fields).
- `email_get_client_thread_messages` - Get messages from thread by id. All messages are fully fetched. For accounts with CacheFile only cached messages will be returned.
- `email_delete_client_thread` - Delete thread by id. All messages from thread will also be deleted.
- `email_get_ai_name_parse` - Parse name to components.
- `email_get_ai_name_genderize` - Detect person's gender from name string.
- `email_get_ai_name_complete` - The call proposes k most probable names for given starting characters.
- `email_get_ai_name_format` - Formats a person's name in correct case and name order using options for formatting instructions.
- `email_get_ai_name_expand` - Expands a person's name into a list of possible alternatives using options for expanding instructions.
- `email_get_ai_name_match` - Compare people's names. Uses options for comparing instructions.
- `email_get_ai_name_parse_email_address` - Parse person's name out of an email address.
- `email_get_storage_file` - Download file
- `email_delete_storage_file` - Delete file
- `email_update_storage_file_copy` - Copy file
- `email_update_storage_file_move` - Move file
- `email_get_storage_folder` - Get all files and folders within a folder
- `email_update_storage_folder` - Create the folder
- `email_delete_storage_folder` - Delete folder
- `email_update_storage_folder_copy` - Copy folder
- `email_update_storage_folder_move` - Move folder
- `email_get_storage_exist` - Check if storage exists
- `email_get_storage_disc` - Get disc usage
- `email_get_storage_version` - Get file versions
