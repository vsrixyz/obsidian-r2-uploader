# Obsidian R2 Uploader

A minimal Obsidian plugin that automatically uploads **pasted** or **drag-and-dropped** images to [Cloudflare R2](https://developers.cloudflare.com/r2/) and inserts a standard Markdown image link into your note.

- Cloudflare R2 only (S3-compatible API)
- Images only — other file types fall through to Obsidian's default behaviour
- Desktop only

## What it does

- **Paste**: copy an image (screenshot, browser image, etc.) and paste into the editor → uploaded to R2 → `![filename](url)` inserted.
- **Drag & drop**: drag an image from Finder/Explorer into the editor → uploaded to R2 → `![filename](url)` inserted.

While the upload is in flight, a placeholder `![Uploading… (id)]()` is shown at the cursor and replaced with the final link on success. On failure the placeholder is removed and an error notice is shown.

## Cloudflare R2 setup

You need five values. Here's how to get each one.

### 1. Create a bucket

R2 → **Create bucket** → give it a name (e.g. `mangosteen`). This is your **Bucket name**.

### 2. Account ID

R2 → **Overview** → the **S3 API** endpoint on the right reads
`https://<ACCOUNT_ID>.r2.cloudflarestorage.com`. The hex string is your **Account ID**.

### 3. API token → Access Key ID + Secret Access Key

R2 → **Manage R2 API Tokens** → **Create API Token**:

- **Permissions:** `Object Read & Write` (not Admin — least privilege).
- **Bucket scope:** restrict to the single bucket you created (not "all buckets").
- Click **Create**.

The result screen shows the **Access Key ID** and the **Secret Access Key**.
⚠️ The secret is shown **only once** — copy it immediately.

> Use the **Access Key ID / Secret Access Key** pair (the S3-style credentials), not the "Token value" shown at the top of that page.

### 4. Public base URL

Your bucket → **Settings** → **Public access**. Either:

- **r2.dev subdomain** — quick, gives `https://pub-xxxxxxxx.r2.dev`. Rate-limited; fine for testing.
- **Custom domain** — connect a domain that's on Cloudflare (e.g. `https://img.example.com`). Cleaner URLs, no rate limit. Recommended.

Copy whichever URL you enable — this is your **Public base URL**. Include the `https://` scheme.

> Tip: visiting the bare public URL in a browser and getting R2's branded
> *"Error 404 — Object not found / Is this your bucket?"* page is the **expected**
> success signal — it confirms the domain is wired to the bucket. There's just
> nothing at the root path yet.

## Installation

This plugin is installed manually (not in the community store).

1. Download `main.js` and `manifest.json` from the [latest release](https://github.com/vsrixyz/obsidian-r2-uploader/releases), **or** build them yourself (see [Build](#build)).
2. Create the folder `<your-vault>/.obsidian/plugins/r2-image-uploader/`.
3. Copy `main.js` and `manifest.json` into it.
4. In Obsidian: **Settings → Community plugins** → reload/restart → enable **R2 Image Uploader**.
5. Open the plugin's settings tab and fill in the five values above (plus an optional path template).
6. Click **Test connection** — expect *"R2 connection successful!"* — then paste an image to confirm.

## Settings reference

| Field | Example |
|-------|---------|
| Account ID | `abc123def456...` (32 hex chars) |
| Bucket name | `my-images` |
| Access Key ID | `AKIAEXAMPLE...` |
| Secret Access Key | `wJalrEXAMPLEKEY...` (masked in the UI) |
| Public base URL | `https://img.example.com` |
| Path template | `obsidian/{year}/{timestamp}-{fileName}.{ext}` |

### Path template variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{year}` | 4-digit year | `2026` |
| `{month}` | 2-digit month | `07` |
| `{day}` | 2-digit day | `03` |
| `{timestamp}` | Unix timestamp (ms) | `1751520000000` |
| `{fileName}` | Sanitised original filename | `screenshot` |
| `{ext}` | File extension | `png` |

Only these tokens are substituted; anything else is left in the path literally.

## Security

- The only external requests go to the Cloudflare R2 S3 endpoint
  (`https://<account-id>.r2.cloudflarestorage.com`) over HTTPS. No other service is contacted.
- Credentials are stored in `<vault>/.obsidian/plugins/r2-image-uploader/data.json`
  in **plaintext** (standard for Obsidian plugins). The Secret Access Key is masked in the UI
  and never written to logs or error messages.
- **If your vault is synced** (git / iCloud / Obsidian Sync / Dropbox), add
  `.obsidian/plugins/r2-image-uploader/data.json` to that tool's ignore list so the
  secret doesn't travel.
- Scope the API token to a single bucket with `Object Read & Write` only.

## Build

Requires Node 18+ and npm.

```bash
npm install
npm run build   # produces main.js
```

For development with auto-rebuild:

```bash
npm run dev
```

## License

[MIT](LICENSE)

## Credits

Originally based on [aest3ra/obsidian-r2-auto-upload](https://github.com/aest3ra/obsidian-r2-auto-upload).
