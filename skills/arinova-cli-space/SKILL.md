---
name: arinova-cli-space
description: Create, validate, preview, publish, rescan, roll back, and monetize Arinova managed Space bundles with @arinova-ai/cli. Use for Space 上架 or 發佈, space.json, OAuth Client ID mismatches, bundle scans, versions, products, storage, and confidential service clients.
---

# Publish a Managed Space

Prerequisite refresh:

1. Install with `npm install -g @arinova-ai/cli`.
2. Run `arinova auth login` and finish browser authorization.
3. Put `--profile <profile>` on every authenticated command; there is no default profile.

## Protect the two-ID boundary

This is the most important Space rule:

- `space.json.id` is the lowercase OAuth Client ID created by `app create`.
- `<space-uuid>` is the separate Space resource UUID returned by `space create` and used in CLI paths.

Never put the Space UUID in `space.json.id`, and never use the OAuth Client ID where a command asks for `<space-id>`.

## First publish

Create the OAuth app before the Space bundle. The redirect URI must be a real callback owned by the creator.

```sh
arinova --profile <profile> app create \
  --name "My Space" \
  --client-id my-space \
  --redirect-uri "https://creator.example/callback"

arinova --profile <profile> space create --name "My Space"
arinova --profile <profile> space init my-space
cd my-space
```

Edit `space.json`: replace the placeholder `id` with `my-space`, confirm the entry file, scopes, and declared API origins, and leave only supported manifest keys. Then validate and package it:

```sh
arinova --profile <profile> space build
```

The default artifact is `dist/<oauth-client-id>-<manifest-version>.zip`. Use the Space UUID returned by `space create` for the remote lifecycle:

```sh
arinova --profile <profile> space version create <space-uuid> \
  --bundle dist/my-space-1.0.0.zip
arinova --profile <profile> space version preview <space-uuid> <version-uuid>
arinova --profile <profile> space version publish <space-uuid> <version-uuid>
```

Use `--json` when IDs must be captured reliably. Preview URLs expire after 15 minutes. Inspect the preview and scan result before publishing.

## Version and scan discipline

Every upload needs a previously unused semantic version. A repeated version returns `SPACE_VERSION_EXISTS`; increment `space.json.version`, rebuild, and upload the replacement.

High-risk content is rejected and remains unlisted. Use `space version scan` to inspect redacted findings. Use `space version rescan` only after an underlying scanner correction; a content correction requires a new immutable bundle and bumped version.

`space version rollback` is not a pointer flip. It rescans, revokes Space OAuth tokens, and disconnects online players before activating the older bundle. Confirm that disruption explicitly.

Read [Managed Space rules](references/space.md) for the closed manifest key set, bundle limits, origin matching, scopes, retention, commerce, storage, and confidential service clients. Use the basics entry's error table for stable-code recovery.
