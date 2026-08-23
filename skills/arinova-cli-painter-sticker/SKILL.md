---
name: arinova-cli-painter-sticker
description: Create and publish Arinova Painter albums or sticker packs with @arinova-ai/cli, including managed cover and gallery uploads, prompts, pricing readiness, sticker review, and CLI 0.2.1 multipart fixes. Use for Painter or sticker 上架, 發佈, 創作者, album readiness, covers, images, or review submission.
---

# Publish Painter Albums and Sticker Packs

Prerequisite refresh:

1. Install with `npm install -g @arinova-ai/cli` and require CLI 0.2.1 or newer for managed Painter covers and corrected sticker uploads.
2. Run `arinova auth login` and finish browser authorization.
3. Put `--profile <profile>` on every authenticated command; there is no default profile.

Choose exactly one track. Painter album IDs and sticker pack IDs are unrelated.

## Painter album happy path

Create a draft and capture its album UUID:

```sh
arinova --profile <profile> painter create \
  --name "Watercolor Portraits" \
  --description "Soft portrait style" \
  --category watercolor \
  --price-type free
```

Supply both a managed cover and at least one gallery image, then configure the album generation instructions:

```sh
arinova --profile <profile> painter upload-cover \
  --id <album-uuid> --file cover.png
arinova --profile <profile> painter upload-image \
  --id <album-uuid> --file sample.png --caption "Example result"
arinova --profile <profile> painter set-prompt \
  --id <album-uuid> --prompt "Create a soft watercolor portrait."
```

For a paid album, set `--price-type credits` with a positive `--price-amount`. Publish through the legacy-compatible update surface:

```sh
arinova --profile <profile> painter update --id <album-uuid> --public true
```

If the server returns `PAINTER_ALBUM_NOT_READY`, use `details.missing`: `cover`, `gallery`, `systemPrompt`, and `price` map directly to the steps above. A safety result may leave the album blocked or pending instead of public; preserve that state.

Painter intentionally uses the legacy `/api/painter` service surface because no equivalent public v1 creator contract exists. Do not rewrite its paths or assume v1 behavior. Read [Painter details](references/painter.md) for readiness, review states, limits, and compatibility.

## Sticker pack happy path

Create the pack and capture its UUID:

```sh
arinova --profile <profile> sticker create \
  --name "Friendly Reactions" \
  --description "Everyday reaction stickers" \
  --price 0
```

Add at least one sticker and an explicit cover, then submit the immutable review candidate:

```sh
arinova --profile <profile> sticker upload-image <pack-uuid> sticker.png
arinova --profile <profile> sticker cover <pack-uuid> --file cover.png
arinova --profile <profile> sticker submit-review <pack-uuid>
```

CLI 0.2.0 sent the wrong multipart field for sticker images and covers. Do not use it for this flow; 0.2.1 sends the server-required `file` field.

There is no supported direct sticker `publish` command. Approval activates the reviewed managed pack. Do not modify a pack while it is pending review; approved content changes invalidate the approval and require another submission. Read [Sticker details](references/sticker.md) for readiness and review behavior.
