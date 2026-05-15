# Google Workspace Extension (OAuth for Personal Accounts)

## Credits

Original project created by [Geun-Oh](https://github.com/Geun-Oh). This fork builds on that work and keeps attribution to the original author.

Extension file location:
- `~/.pi/agent/extensions/google-workspace/index.ts`

Local OAuth credential storage:
- `~/.pi/agent/google-workspace/oauth.json`

## 1) Google Cloud Setup

1. Create a project in Google Cloud Console.
2. Enable these APIs:
   - **Google Drive API**
   - **Google Docs API**
   - **Google Slides API**
   - **Google Sheets API**
3. Configure the OAuth consent screen.
   - Add your account as a test user if your app is in testing mode.
4. Create an OAuth client.
   - Application type: **Desktop app**
   - Copy the Client ID and Client Secret.
   - Do not configure or type a redirect URI. The extension automatically creates a temporary loopback redirect such as `http://127.0.0.1:<random-free-port>/oauth2callback` during `/gws-setup`.

## 2) Initial Authentication in pi

1. Run `/reload` in pi.
2. Run `/gws-setup`.
3. Enter your Client ID and Client Secret.
4. Sign in with Google and approve permissions.
5. On success, `oauth.json` is saved.

## 3) Available Tools

### Drive
- `google_drive_list`
- `google_drive_download`
- `google_drive_upload`
- `google_drive_create_folder`

### Docs
- `google_docs_read`
- `google_docs_create`
- `google_docs_append_text`
- `google_docs_replace_all_text`
- `google_docs_download` (saves local files in `pdf|docx|md|txt|rtf|odt|html_zip`)
  - `md` conversion preserves headings, lists, tables, and inline emphasis as much as possible.

### Sheets
- `google_sheets_create`
- `google_sheets_read`
- `google_sheets_update_values`

### Slides
- `google_slides_read`
- `google_slides_replace_text`

### Status
- `google_workspace_status`

### Download Example

`google_docs_download` accepts:
- `documentId`: Google Docs document ID
- `format`: `pdf` / `docx` / `md` / `txt` / `rtf` / `odt` / `html_zip`
- `outputPath` (optional): target local path (if omitted, it saves to the current working directory with an auto-generated name)

## 4) Sign Out

- Run `/gws-logout`
- The command attempts to revoke the Google token before deleting the local credential file.

## Notes

- OAuth uses PKCE, a random local loopback callback port, and validates the callback `state` value.
- A `refresh_token` must be issued on first consent for automatic token refresh.
- If no `refresh_token` exists, run `/gws-setup` again.
- Old refresh tokens are reused only when the OAuth client ID and client secret are unchanged.
- This version includes Drive, Docs, Slides, and Sheets scopes. If you have an older token, re-run `/gws-setup` and re-consent.
