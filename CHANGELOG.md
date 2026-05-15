# Changelog

## 1.2.0 - Desktop OAuth loopback flow

- Reworked `/gws-setup` for Google OAuth Desktop app clients.
- Removed the manual redirect URI prompt.
- Removed the fixed callback port (`53682`).
- Added automatic loopback redirect URI creation using a random free local port:
  - `http://127.0.0.1:<random-free-port>/oauth2callback`
- Kept PKCE (`S256`) and OAuth `state` validation.
- Started the local callback server before opening the browser.
- Updated refresh-token reuse logic for dynamic redirect URIs.
- Updated README and setup notes to explain the Desktop app OAuth flow.

## 1.1.0 - OAuth hardening and attribution

- Added credits to the original creator, Geun-Oh.
- Updated Pi extension imports to current package names:
  - `@earendil-works/pi-coding-agent`
  - `typebox`
- Added PKCE support.
- Improved OAuth callback handling and manual fallback.
- Improved `/gws-logout` to attempt token revocation before deleting local credentials.
- Added warning when Google does not return a refresh token.
