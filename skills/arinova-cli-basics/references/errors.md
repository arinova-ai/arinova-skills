# Error recovery

Keep the original error code and structured details. Apply the narrow recovery below, then rerun only the failed step.

| Code or status | Meaning | Recovery |
| --- | --- | --- |
| `SPACE_OAUTH_APP_REQUIRED` | The Space has no owned OAuth app matching the manifest. | Create the app, put its Client ID in `space.json.id`, rebuild, and upload a new version. |
| `SPACE_OAUTH_APP_MISMATCH` | The manifest Client ID differs from the app already bound to the Space. | Restore the originally bound Client ID; do not put the Space UUID in `space.json.id`. |
| `SPACE_VERSION_EXISTS` | The manifest version was already uploaded for this Space. | Increment `space.json.version`, rebuild, and create a new version. |
| `SPACE_VERSION_NOT_PUBLISHABLE` | The chosen version is not in a publishable scan state. | Inspect `space version scan`; rescan after a scanner correction or upload corrected content with a new version. |
| `SPACE_ACTIVE_VERSION_DELETE_DENIED` | The requested version is active. | Publish or roll back to another version before deleting it. |
| `INVALID_SPACE_BUNDLE` | Server bundle validation failed. | Preserve the server reason, run `space build`, and fix the reported manifest, path, type, count, or size rule. |
| `THEME_REVIEW_REQUIRED` | The theme is not approved. | Wait for approval; if it is blocked, an administrator must resolve the review before publish can succeed. |
| `THEME_SAFETY_REVIEW_REQUIRED` | Upload scanning found high risk and queued review. | Do not repeatedly upload the same bundle. Review the finding and await the administrative decision or upload corrected content. |
| `PAINTER_ALBUM_NOT_READY` | Publication prerequisites are missing. | Read `details.missing`; supply `cover`, `gallery`, `systemPrompt`, and a positive paid `price` as listed. |
| `CONFIRMATION_REQUIRED` | A non-interactive side effect lacked acknowledgement. | Confirm the exact target and requested change with the creator, then rerun that operation with `--yes`. |
| `CONFIG_ERROR` | The profile, endpoint configuration, or config file is invalid. | Select an existing profile, correct the endpoint, or repair the preserved malformed-config backup before retrying. |
| HTTP 429 | The active rate-limit bucket is exhausted. | Honor `Retry-After`, reduce concurrency, and continue later. Upload routes default to 15 per minute. |

For unknown codes, do not replace the server message with a guess. Report the command path, endpoint environment, HTTP status, stable code, and redacted details.
