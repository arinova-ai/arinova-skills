# Managed Space rules

## Manifest identity and keys

`space.json` is strict. Unknown properties fail validation. The closed key set is:

| Key | Rule |
| --- | --- |
| `id` | Required OAuth Client ID; 1–128 lowercase ASCII letters, digits, or internal hyphens; exact owned-app match. |
| `version` | Required semantic version; unique within the Space. |
| `entry` | Required safe relative HTML path included in the bundle. |
| `name` | Optional string used by the runtime. |
| `description` | Optional string used by the runtime. |
| `assets` | Optional list of at most 512 included safe relative paths. |
| `declaredApiOrigins` | Optional list of at most eight unique bare HTTPS origins. |
| `requestedScopes` | Includes `profile`; may contain only `profile`, `agents`, and `economy`, without duplicates. |

The manifest ID stays bound to the first OAuth app used by the Space. The Space resource UUID remains separate for Creator Console, CLI arguments, and API paths.

## Origins and scopes

The managed iframe has an opaque origin. CSP `self` does not authorize the Arinova API, so declare the exact API origin used in the target environment plus every other origin contacted by fetch, WebSocket, or SDK calls. Each value must be origin-only: no path, query, fragment, credentials, or plain HTTP. Declaring an origin does not bypass the remote service's CORS policy.

`profile` is the baseline embedded scope. Add `agents` or `economy` only when the product needs them. Email and arbitrary scopes are not valid embedded Space scopes. Design a usable fallback when the user declines an elevated scope.

## Bundle validation

The ZIP root must directly contain `space.json`; do not archive a containing directory. Nested application files are allowed. Symlinks, special files, unsafe path segments, path-prefix conflicts, and HTML `<base>` elements are rejected.

Current limits:

- uploaded archive: 20 MiB;
- manifest: 256 KiB;
- all uncompressed content: 40 MiB;
- one file: 10 MiB;
- entries: 512;
- declared origins: 8.

Allowed extensions are HTML, JavaScript modules, CSS, JSON, common raster and SVG images, web fonts, common audio, and WebAssembly. Archives, source maps, executables, icons, hidden development files, and extensionless files are not accepted. `space build` mirrors these checks, but the server validates and scans again.

## Versions, retention, and recovery

Uploads begin as draft. The platform retains the active version and newest inactive versions up to five total, while preserving pending-review versions as required. The active version cannot be deleted.

A high-risk scan rejects a version and keeps the Space unlisted. After a scanner false-positive is corrected, `space version rescan <space-uuid> <version-uuid>` can return that immutable version to draft. If content changes, bump the manifest version and upload a new bundle.

Publishing and rollback both run current scan rules, revoke Space OAuth tokens, and disconnect online players. Treat rollback as a disruptive publish operation.

## Products, storage, and service clients

Creator products are managed under `space products`. A Space can define up to 100 consumable, durable, or subscription products. Deactivation stops new purchases but existing subscriptions continue; `wind-down` also schedules subscription renewals to end at the current period boundary. Inventory and entitlements are server-authoritative; per-user Space storage is not an inventory ledger.

Storage commands require a Space-bound OAuth access token, not a creator profile key.

For a trusted backend, create a confidential OAuth app with explicit service scopes, for example:

```sh
arinova --profile <profile> app create \
  --name "My Space Service" \
  --client-id my-space-service \
  --redirect-uri "https://creator.example/service-callback" \
  --confidential \
  --allowed-scopes profile,wager,llm
```

The client secret is shown once. Keep it only on the backend. The server-side Spaces SDK exchanges it for scoped service tokens; never embed the secret or a service token in a Space bundle.
