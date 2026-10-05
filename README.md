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

A dark **vault glass** theme: a deep midnight base with soft gold, cyan and
violet blooms behind it, and frosted glass surfaces on top — blurred on the few
large surfaces (cover, top bar, modals, category tabs) and
translucent-but-unblurred on entry cards, so long lists stay fast and the mono
field values stay crisp. A single gold accent carries the registry identity;
coral marks anything destructive. Where `backdrop-filter` is unavailable,
surfaces fall back to opaque via `@supports`. The palette is deliberately cool
and low-key — a private numbers vault should read as secure and discreet, not
decorative. The glass surface treatment is shared with the RACK project, so the
two apps feel like a family.

Behind the glass is an animated mesh gradient, in the manner of the Jitter
"Gradient Background" template: five soft colour nodes drifting around the dark
base, each breathing its own hue, so the composition never quite repeats. It is
drawn into a canvas about an eighth of the screen size and stretched to fill the
view — the upscale does the blurring, so a frame costs a few thousand pixels of
gradient fill rather than a full-screen repaint, and the nodes are laid down
additively, as light on top of the page, so the ledger dot-grid still reads
through the mesh.

The mesh drifts with the pointer and floats on its own when it goes still. It
deliberately stops tracking over text fields and while a record modal is open — a
moving backdrop behind something you are typing into or reading costs more than
it adds — and it holds still entirely under `prefers-reduced-motion`. If the
canvas cannot be created at all, a static gradient stands in.

The **lock screen** has its own backdrop instead: a particle field adapted
from [Spipa circle](https://codepen.io/alexandrix/pen/oQOvYp) by Alex Andrix.
Each particle is spring-coupled to an attractor spot on a coarse grid; the
attractor wanders toward whichever neighbouring spot has the highest radial field
value, particles die when they get stuck or grow too old, and a fading fill draws
the trails. It runs across the whole lock screen — the chooser, the create-vault
form and the open-an-existing-vault form all share it — and stops when you enter
a vault, handing the screen over to the mesh. It takes the place of the mesh
there rather than layering over it: two animated backdrops stacked would be
muddy, and only one is ever drawn. Locking again brings the field back, carrying
on from where it left off rather than restarting.

The field draws the **outline of the card in front of it**, not a disc in the
middle of the screen. Getting there needed more than reshaping the field, and the
reason is worth recording: the attractor walk only ever compares four neighbours
against `chaos * random()`, so its drift per step is small. Measured, the field's
gradient was 13.4 per grid step near the crest and 4.0 far out — both below the
original `CHAOS` of 30, meaning the chaos outvoted the field everywhere and the
walk was really just a random walk. Tuning the field could not fix that (it was
flat across `FIELD_WEIGHT` 3→30 and `CHAOS` 30→4), so each particle is now also
pulled gently toward the nearest point of the ring. That makes the shape
deterministic and leaves the spring, jitter and trails as they were.

Two changes from the original: the palette is the app's own — a narrow
analogous band from violet to cyan (~60°, rather than the app's full ~290°
spread, which reads as confetti at this density), with the variety coming from
lightness instead, every particle taking a shade between 12% and 78% so the field
runs dark to light, and each hue breathing ±8° around its base rather than
sweeping the whole colour wheel. And the field is drawn at ~55% scale and
stretched, so the trails stay soft and a frame costs a fraction of full size.

## Using it

1. **Start a new vault** — give it a name and set a password (minimum 8 characters).
   A new vault begins with two categories: *Vehicles* and *IDs & Licenses*.
2. **Add a category** — click *+ New category* for anything else you track
   (warranties, appliances, subscriptions, property, accounts). The `×` on a tab
   deletes that category **and every record in it**, so the confirmation states
   the record count first. There is no undo.
3. **Add a record** — a title plus any number of custom fields. Fields are
   key/value pairs, so the same record type can hold anything. The two built-in
   categories pre-fill sensible fields (VIN, licence plate, insurance policy for
   vehicles; document number, issued, expires for IDs). A field that holds a
   date — *Issued*, *Expires*, or any field named like a date (dob, start, end,
   renewal, valid until …) — renders a native date picker rather than a free-text
   box, so the value is always stored in one predictable format.
4. **Attach photos** — in the record modal, add images of the document itself.
   See [Images](#images) below for what happens to them.
5. **Save vault file** — writes an encrypted `.vault` file. If your browser
   supports the File System Access API (Chrome, Edge, other Chromium browsers),
   it saves back to the same file you chose; otherwise it downloads a new copy.
6. **Open an existing vault** — pick the `.vault` file and enter its password.

The *Unsaved changes* dot in the header, plus the browser's own beforeunload
warning, are the only guards against losing edits.

## Reading values

**Field values and thumbnails are blurred until you hover or focus the card.**
This is deliberate: these are the sensitive numbers the vault exists to hold, and
the default view should not expose them to a passer-by, a screen share, or a
photo of your monitor.

- Hover a card, or tab to it, to reveal that card.
- **Reveal values** in the header clears the blur everywhere, for when you are
  deliberately reading the vault.
- Locking re-blurs everything, so nothing stays readable after you walk away.

Blur is a display convenience, not a security boundary — the values are in the
page's memory while unlocked, exactly as before. It protects against casual
exposure, not against someone using developer tools on an unlocked session.

## Images

Photos can be attached to any record — a licence, a passport page, a policy
document — and are stored **inside the same encrypted envelope as the text**, so
a photo is protected exactly like a VIN.

They are downscaled on import to a maximum of 1600px on the long edge and
re-encoded as JPEG at quality 0.8. Two consequences worth knowing:

- **Size.** A typical 4 MB phone photo becomes roughly 200–400 KB, so a vault
  with twenty photos stays around 6 MB rather than 100 MB. Saves stay fast.
- **EXIF is stripped.** Because the pixels are re-encoded, location and device
  metadata are discarded. Phone photos of identity documents frequently carry
  GPS coordinates; those do not survive into the vault.

The original file is never stored or modified — only the downscaled copy goes
in. Images appear as blurred thumbnails on the card; click one to open it
full-size, and press Escape or click outside to close.

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
- The only network requests the page makes are for the Google Fonts stylesheet,
  the font files, and `VERSION` — the version number shown on the lock screen
  and in the header. All of them happen before you enter a password and carry
  no vault data. Everything else — encryption, decryption, file reading, file
  writing — is local.

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
- Images make the vault meaningfully bigger than a text-only one. A forgotten
  password now also means losing photographs you may not have backed up
  elsewhere, so keep the backup copy current if you use them.
- Blur is presentation only. It does not encrypt anything on its own and can be
  bypassed from developer tools while the vault is unlocked.

## Versioning

The current version lives in `VERSION` at the repo root and follows
`MAJOR.MINOR.PATCH`. Every commit on `main` bumps it, in the same commit as the
change. The app reads that file at load and shows it as a `v<number>` badge on
the lock screen and in the header, so bumping the file is all that is needed —
the number is never written into `index.html`. Opened straight off the
filesystem the badge stays blank, because a browser cannot fetch a sibling file
from `file://`. The full rules are in [`VERSIONING.md`](VERSIONING.md).

## Files

- `index.html` — the entire application: markup, styles, and logic in one file.
- `VERSION` — the current version; the single source of truth.
- `VERSIONING.md` — when and how the version is bumped.

## Browser support

Needs Web Crypto (`crypto.subtle`) — all current browsers. The in-place save via
File System Access API is Chromium-only; everywhere else the save falls back to a
normal file download, and opening a vault uses the standard file picker.