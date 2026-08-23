# Sticker details

## Minimum review candidate

A managed sticker pack needs:

- an owned managed pack record;
- a managed cover asset;
- at least one sticker;
- a managed image asset for every sticker;
- agent prompts for every sticker when the pack is marked agent-compatible.

The first uploaded sticker may supply a cover automatically when none exists, but an explicit `sticker cover` command makes the intended storefront image clear.

## Review lifecycle

`sticker submit-review <pack-uuid>` sets the pack to draft plus `pending_review`. The creator cannot modify it while review is pending. Administrative approval makes a managed pack active and publicly discoverable.

Direct `sticker publish` and `sticker unpublish` commands intentionally fail locally because the public contract is review-driven. Do not substitute another request.

Changing an approved pack's cover, images, or reviewed content returns it to draft, clears approval, and may remove it from public discovery until a new review succeeds. Existing owners can retain access while storefront availability changes.

## Upload behavior

Sticker images and covers must use the multipart field named `file`. CLI 0.2.1 corrects both paths; CLI 0.2.0 is not suitable for creator sticker uploads.

Uploads share the default file-upload limit of 15 per minute. Honor HTTP 429 timing and send images sequentially or with bounded concurrency. A review candidate with no sticker, no cover, or an incomplete managed asset is rejected before review.

Pack price is an integer from zero through the server maximum. Zero is free. Managed pack categories and additional agent-compatible metadata may be richer in the web creator editor than the current CLI surface; preserve server-returned fields when inspecting or updating a pack.
