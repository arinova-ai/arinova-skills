# Painter details

## Publication readiness

`painter update --id <album-uuid> --public true` checks the effective album state before publication:

| Missing value | Required action |
| --- | --- |
| `cover` | Upload a managed cover with `painter upload-cover`. |
| `gallery` | Upload at least one managed gallery image with `painter upload-image`. |
| `systemPrompt` | Set non-empty instructions with `painter set-prompt`. |
| `price` | For `credits`, set a positive `priceAmount`; free albums may remain zero. |

The gallery limit is 50 images. Creator uploads use the shared default file-upload limit of 15 per minute.

## Review states

Owner responses expose explicit state:

- `draft`: private and editable;
- `pending_review`: submitted and awaiting a decision;
- `blocked`: listing safety prevented publication;
- `published`: public catalog listing;
- `report_suspended`: removed after reports crossed the enforcement threshold.

A publication request can return HTTP 202 with a review case rather than becoming public. Do not describe that as success. While a review is pending, another publish request can return `PAINTER_ALBUM_REVIEW_PENDING`.

## Pricing and generation

Free albums use `--price-type free`. Paid albums use `--price-type credits --price-amount <positive-integer>`. Customer generation can accept a single prompt or a batch of at most ten through product surfaces; generation quotas and final settlement remain server-authoritative.

Completed paid customer generations use the platform's creator revenue share. Foreground polling can time out before a generation reaches a final state; history and the server sweeper remain authoritative.

## Compatibility boundary

Painter commands are an explicit legacy compatibility surface under `/api/painter`. The CLI keeps these paths because there is no equivalent public v1 creator contract. Preserve the response's `reviewStatus`, managed image identifiers, page values, and errors rather than projecting a newer resource shape onto them.

The managed cover endpoint is separate from gallery upload. `upload-image` alone cannot satisfy the `cover` readiness check; use `upload-cover` as well.
