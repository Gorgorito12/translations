# AoE3 Mod Launcher — community translations

This repo hosts **community translation packs as files on `main`**. The
[AoE3 Mod Launcher](https://github.com/Gorgorito12/AoE3-Mod-Launcher) reads it
directly — no GitHub releases needed. Mods point here via
`translations.folderRepo` in their `mod.json` in the
[mods catalog](https://github.com/Gorgorito12/aoe3-mods-catalog).

## Structure

Each translation keeps a **version history** under one folder per version:

```
translations/
  <id>/                       # id = the language pack (es, es-419, fr, …)
    <version>/                # one subfolder per version (1.2, 1.1, …)
      translation.json        # the manifest (contentHash + zip + date)
      <id>.zip                # the translated files
schema/
  translation.schema.json     # the translation.json format (for editors / CI)
```

- One folder per language; inside, **one subfolder per version**. The launcher
  groups them into a single menu entry with a **version picker** (it shows the
  latest 10 versions; older ones stay in git but aren't listed).
- **Single-version (legacy) is still fine:** `translations/<id>/translation.json`
  directly (no version subfolder) is read as one version.

## How to publish a pack

1. In the launcher, open **Settings → Packager**, pick your mod and source files,
   and export. You get a `translation.json` + a `.zip`.
2. Create `translations/<id>/` here and **commit both files** into it (push to
   `main` or open a PR). That's it — no release, no separate asset upload.
3. Players see the pack the next time their launcher refreshes the language list.

## Updating / adding a new version

Re-export with the Packager (bump the version) and **commit the new
`translations/<id>/<version>/` subfolder** — never touch the old ones. The history
accumulates append-only. The packager bakes a `contentHash` + `date` into each
manifest; the launcher uses the newest version for the menu/notification and shows
the rest in the version picker so users can roll back. (If you'd rather keep a
single live version, just commit over the same files — both work.)

## How the launcher finds packs

- Reads the whole tree in ONE call (Git Trees API) and each
  `translations/<id>[/<version>]/translation.json` via the raw CDN; downloads the
  `.zip` from
  `raw.githubusercontent.com/Gorgorito12/translations/main/translations/<id>/<version>/<zip>`.
- Dedups / notifies on `id@contentHash` of the NEWEST version. The same key is
  computed by the central notifier service, so a pack alerts once across the
  whole community.
- The MD5 hashes in `translation.json` are the real compatibility check at apply
  time; `compatibleWith` is the translator's tested-versions hint.

## Notes

- Legacy packs published as **GitHub releases** (on the old `papillo12/translations`)
  still work — the launcher reads both in dual mode — so migration can be gradual.
- `schema/translation.schema.json` documents every field. Don't hand-write the
  hashes — let the Packager compute them.
- `.zip` files are marked **binary** in `.gitattributes`; don't change that or the
  archives corrupt on commit.

Apache-2.0.
