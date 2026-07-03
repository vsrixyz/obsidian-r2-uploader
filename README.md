# Obsidian R2 Uploader

Ever paste a screenshot into a note and end up with a messy pile of image files in your vault? This plugin fixes that. When you paste or drag an image into a note, it uploads the image to your own [Cloudflare R2](https://developers.cloudflare.com/r2/) storage and inserts a normal image link — your vault stays light and your images live in the cloud.

## What it does

Paste or drag an image into the editor. A brief `Uploading…` placeholder appears, then becomes a Markdown image link pointing at your R2 bucket. Images only — anything else pastes as usual. Desktop only.

## What you need (prerequisites)

- A [Cloudflare account](https://dash.cloudflare.com) (free tier is fine)
- An R2 bucket to store images in
- An R2 API token with **Object Read & Write** on that bucket
- A public URL for the bucket — either a free `r2.dev` subdomain or your own domain (optional, but nicer links)
- Obsidian on desktop

## How to install

**Step 1 — Gather five values from Cloudflare.** In the dashboard, open **R2**:

1. **Bucket name** — **Create bucket**, name it (e.g. `my-notes-images`).
2. **Account ID** — on the R2 Overview page, the S3 API address reads `https://<ACCOUNT_ID>.r2.cloudflarestorage.com`; the `<ACCOUNT_ID>` part is it.
3. **Access Key ID** and **Secret Access Key** — **Manage R2 API Tokens → Create API Token**, set **Object Read & Write**, restrict it to your bucket, **Create**. The secret shows once, so copy it now.
4. **Public base URL** — bucket **Settings → Public access**. Enable the `r2.dev` subdomain (`https://pub-xxxx.r2.dev`) or connect a custom domain (`https://images.example.com`). Keep the `https://`.

**Step 2 — Install the plugin files.**

1. Download `main.js` and `manifest.json` from the [latest release](https://github.com/vsrixyz/obsidian-r2-uploader/releases).
2. In your vault, create `.obsidian/plugins/r2-image-uploader/` and drop both files in. (The `.obsidian` folder is hidden — on Mac, `Cmd+Shift+.` shows hidden files in Finder.)
3. In Obsidian, open **Settings → Community plugins**, reload or restart, then enable **R2 Image Uploader**.

**Step 3 — Configure and test.** Open the plugin's settings, paste in your five values, and click **Test connection**. Once it passes, paste an image into a note.

## Settings

| Setting | What it is |
|---------|-----------|
| Account ID | Your Cloudflare account ID. |
| Bucket name | The bucket images upload to. |
| Access Key ID | The public half of your R2 token. |
| Secret Access Key | The private half — hidden in the input. |
| Public base URL | The address your images load from. |
| Path template | Folder and filename pattern in the bucket. |

The path template controls where files land. Default: `obsidian/{year}/{timestamp}-{fileName}.{ext}` → `obsidian/2026/1751520000000-screenshot.png`. Available pieces: `{year}` `{month}` `{day}` (date), `{timestamp}` (unique number), `{fileName}` (cleaned original name), `{ext}` (file type).

## Security

- The plugin only contacts your own Cloudflare R2 storage — no other service.
- Your keys are stored on your machine in the plugin's `data.json` in plain text (standard for Obsidian plugins). The secret is masked in the settings UI.
- If your vault syncs (iCloud, Obsidian Sync, Dropbox, git), that file travels with it. To keep the secret local, exclude `.obsidian/plugins/r2-image-uploader/data.json` from sync.
- Scoping the token to one bucket with Read & Write limits the damage if the key ever leaks.

## License & credits

[MIT](LICENSE). Based on [aest3ra/obsidian-r2-auto-upload](https://github.com/aest3ra/obsidian-r2-auto-upload).
