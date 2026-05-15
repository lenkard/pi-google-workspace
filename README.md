# pi Google Workspace Extension

Google Workspace extension for [pi](https://github.com/badlogic/pi-mono).

## Credits

Original project created by [Geun-Oh](https://github.com/Geun-Oh). This fork builds on that work and keeps attribution to the original author.

Features:
- Google Drive (list, upload, download, create folder)
- Google Docs (read, create, append, replace, export)
- Google Sheets (create, read, update)
- Google Slides (read, replace text)
- OAuth setup helpers (`/gws-setup`, `/gws-logout`, `google_workspace_status`)

## Install

### npm
```bash
pi install npm:pi-google-workspace
```

Then run:
```bash
/gws-setup
```

## OAuth and Google Cloud Console Setup (Detailed)

This extension uses OAuth 2.0 and requires Google Workspace APIs to be enabled in your Google Cloud project.

### 1) Create or select a Google Cloud project

1. Open [Google Cloud Console](https://console.cloud.google.com/).
2. Click the project selector in the top bar.
3. Create a new project (or select an existing one).
4. Make sure billing/org policies allow API access for this project.

### 2) Enable required APIs

Go to **APIs & Services → Library**, then enable all of the following:

- **Google Drive API**
- **Google Docs API**
- **Google Slides API**
- **Google Sheets API**

Tip: You can verify from **APIs & Services → Enabled APIs & services**.

### 3) Configure OAuth consent screen

Go to **APIs & Services → OAuth consent screen**:

1. Choose **External** (personal Gmail) or **Internal** (Google Workspace org only).
2. Fill required app info:
   - App name
   - User support email
   - Developer contact email
3. Add scopes (minimum required by this extension):
   - `https://www.googleapis.com/auth/drive`
   - `https://www.googleapis.com/auth/documents`
   - `https://www.googleapis.com/auth/presentations`
   - `https://www.googleapis.com/auth/spreadsheets`
4. If app is in **Testing**, add your Google account under **Test users**.
5. Save and continue.

> Note: Sensitive/restricted scopes may require verification for production/public distribution. For personal use in testing mode, test users are usually enough.

### 4) Create OAuth client credentials

Go to **APIs & Services → Credentials → Create Credentials → OAuth client ID**.

Recommended option:

- **Application type:** Desktop app
- Desktop clients work well with loopback redirect URIs such as `http://127.0.0.1:53682/oauth2callback`.

Alternative option:

- **Application type:** Web application
- Add this authorized redirect URI exactly:
  - `http://127.0.0.1:53682/oauth2callback`

Then copy:
- **Client ID**
- **Client Secret**

### 5) Connect in pi

Run inside pi:

```bash
/reload
/gws-setup
```

Then:
1. Paste Client ID
2. Paste Client Secret
3. Confirm Redirect URI (default is recommended)
4. Complete Google sign-in + consent in browser

The extension uses a local HTTP loopback callback. The redirect URI must use `http://` and a loopback host such as `127.0.0.1` or `localhost`.

### 6) Token storage and refresh behavior

Credentials are stored locally at:
- `~/.pi/agent/google-workspace/oauth.json`

The extension stores `access_token` and `refresh_token` for automatic refresh. OAuth uses PKCE and validates the callback `state` value.
If `refresh_token` is missing (or scopes changed), run `/gws-setup` again and re-consent.

### 7) Common setup issues

- **"redirect_uri_mismatch"**
  - For Web application clients, the redirect URI in Google Cloud must exactly match the one used in `/gws-setup`.
  - Use the default `http://127.0.0.1:53682/oauth2callback` unless you have a reason to change it.
- **"access_denied" or app not available**
  - Your account is not added as a test user (when app is in Testing).
- **API not enabled / 403 errors**
  - One or more required APIs were not enabled in the selected project.
- **No refresh token returned**
  - Re-run `/gws-setup` and grant consent again.
  - If you changed OAuth client ID, secret, redirect URI, or scopes, old refresh tokens are not reused.

## Available Tools

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
- `google_docs_download`

### Sheets
- `google_sheets_create`
- `google_sheets_read`
- `google_sheets_update_values`

### Slides
- `google_slides_read`
- `google_slides_replace_text`

### Status
- `google_workspace_status`

The package gallery reads npm packages that include the `pi-package` keyword.

## Development

- Main extension file: `index.ts`
- Reload in pi: `/reload`
- Re-run setup after scope changes: `/gws-setup`

## Security Notes

- Do not commit OAuth tokens (`oauth.json`).
- Use least-privilege OAuth scopes when possible.
- `/gws-logout` attempts to revoke the Google token before deleting local credentials.
- Revoke credentials from Google account security settings if compromised.
