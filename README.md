# The Registry — Your Numbers Vault

A private, offline ledger for the numbers that matter: vehicle VINs and licence
plates, passport and licence numbers, policy numbers, account references — any
label/value pair you want to be able to find again without storing it in a notes
app or a spreadsheet.

**Live:** <https://aakifahmedd.github.io/personal-vault/>

Everything lives in one encrypted file on your own device. There is no server, no
account, and no network request after the page and fonts load.

## Running it

Open `index.html` in any modern browser, or visit the GitHub Pages URL above.
There is nothing to install and no build step — it is a single self-contained
HTML file.

```bash
open index.html          # macOS
# or drag index.html into a browser window
```

Hosting it on a static host does not make it a hosted service: the page has no
backend, and the vault file never leaves the browser. The hosted copy is just
the same HTML served over HTTPS, which is what Web Crypto requires.

## Interface

Surfaces are frosted glass over a warm brass/rust/ink gradient on the paper base
— blurred on the few large surfaces (cover, top bar, modals, category tabs) and
translucent-but-unblurred on entry cards, so long lists stay fast and the mono
field values stay crisp. Where `backdrop-filter` is unavailable, surfaces fall
back to opaque via `@supports`.

## Using it

1. **Start a new vault** — give it a name and set a password (minimum 8 characters).
   A new vault begins with two categories: *Vehicles* and *IDs & Licenses*.
2. **Add a category** — click *+ New category* for anything else you track
   (warranties, appliances, subscriptions, property, accounts).
3. **Add a record** — a title plus any number of custom fields. Fields are
   key/value pairs, so the same record type can hold anything. The two built-in
   categories pre-fill sensible fields (VIN, licence plate, insurance policy for
   vehicles; document number, issued, expires for IDs).
4. **Save vault file** — writes an encrypted `.vault` file. If your browser
   supports the File System Access API (Chrome, Edge, other Chromium browsers),
   it saves back to the same file you chose; otherwise it downloads a new copy.
5. **Open an existing vault** — pick the `.vault` file and enter its password.

The *Unsaved changes* dot in the header, plus the browser's own beforeunload
warning, are the only guards against losing edits.

## How the encryption works

- Password → key via **PBKDF2-SHA256, 250,000 iterations**, 16-byte random salt.
- Data → ciphertext via **AES-GCM 256**, 12-byte random IV per save.
- File format is a small JSON envelope:

  ```json
  { "app": "registry-vault", "version": 1, "salt": "...", "iv": "...", "data": "..." }
  ```

- The derived key and the plaintext password are held only in memory for the
  duration of an unlocked session, and are dropped on **Lock**.
- All of this uses the browser's built-in Web Crypto API. There is no key
  derivation library, no external dependency, and no code path that transmits
  anything.
- The only network requests the page makes are for the Google Fonts stylesheet
  and font files, which happen before you enter a password and carry no vault
  data. Everything else — encryption, decryption, file reading, file writing —
  is local.

**There is no password reset.** A lost password means a lost vault, and a lost
file cannot be recovered from anywhere else. Keep a backup copy of the `.vault`
file, and store the password somewhere you will still remember it.

## What this is not

It is deliberately a local tool, not a sync service. Trade-offs that come with
that choice:

- No recovery if the file or the password is lost.
- No cross-device access without copying the file yourself (which is a feature,
  but it is manual).
- The password and key live in a JavaScript variable while unlocked, so a
  malicious script or an untrusted browser extension is a real exposure. Only run
  it from a copy you trust.
- This repository is public, so the page's source is readable by anyone. That is
  fine — it is the same HTML you are running — but it means a convincing *fake*
  copy of this app could be published by someone else, and a fake copy could
  exfiltrate a password you type into it. Check the address before entering a
  password, and prefer the `github.io` URL over anything you did not get from
  this repository.
- Entries are per-category and flat; there is no nesting, search, or tagging.

## Files

- `index.html` — the entire application: markup, styles, and logic in one file.

## Browser support

Needs Web Crypto (`crypto.subtle`) — all current browsers. The in-place save via
File System Access API is Chromium-only; everywhere else the save falls back to a
normal file download, and opening a vault uses the standard file picker.