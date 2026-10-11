# Hexorb AI — GitHub Pages + separate backend

This update preserves the existing single-page UI and animations, removes the Firebase SDK/config dependency, adds a Node.js API with password-hashed accounts, signed sessions, persistent SQLite storage, admin roles and server-checked admin endpoints. Chat history and memory are saved locally in the browser in this build; user accounts/admin/feedback/announcements are server-side.

## 1. Deploy the backend

Use a Node.js host that supports a **persistent disk** (SQLite database must survive restarts). Do not use GitHub Pages for the backend.

In `backend/` run:

```sh
npm install
npm start
```

Set environment variables on the host:

- `JWT_SECRET`: random secret, at least 32 characters.
- `OWNER_EMAIL`: your account email. Register with this email; it becomes the initial owner/admin. If the account already exists, restart after setting this variable and it will be promoted.
- `ALLOWED_ORIGINS`: exact GitHub Pages origin, e.g. `https://YOURNAME.github.io` (comma-separated if needed). Leave empty only for a test deployment.
- `DB_PATH`: optional path on the host's persistent disk, e.g. `/data/hexorb.sqlite`.
- `PORT`: usually supplied by the host.

Example secret generation on a trusted terminal: `node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"`.

**Important:** choose a host with HTTPS, persistent disk, backups and Node.js support. Never commit `.env`, database files or secrets to GitHub.

## 2. Point the UI to the API

Edit `backend-config.js` and replace `https://YOUR-BACKEND-DOMAIN/api` with your real backend URL ending in `/api`. Set `owner` to the same email as `OWNER_EMAIL`. This owner string is only a UI hint; the backend checks admin permissions itself.

Upload the contents of `HexorbAI-main/` to the existing GitHub Pages repository, replacing the corresponding files. Do **not** upload the `backend/` folder to the Pages root as a way to run the server; deploy it separately.

## 3. Admin access

Sign in using the `OWNER_EMAIL` account. In Admin → Users, enter an existing registered user's email and press **Make admin** or **Remove admin**. The server verifies the session and admin role before changing roles. A random visitor cannot grant themselves admin through the browser.

## Honest limitations in this build

- Google sign-in is disabled; email/password is used.
- Password-reset endpoint is only a safe placeholder until an email delivery provider is configured; it does not send a reset email yet.
- Email verification is not implemented; the old UI's verification action will report this.
- Chat history and memory are local to each browser/device, not synced to the server.
- The backend has not been deployed to your account/host; set environment variables and test deployment before using real accounts.
