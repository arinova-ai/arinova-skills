---
name: arinova-cli-theme
description: Scaffold, develop, build, upload, review, update, and publish an Arinova Office theme with @arinova-ai/cli. Use for theme 上架 or 發佈, theme.json, theme.js, the port 3100 dev server, flat assets, CSP failures, safety review, or republishing an updated live theme.
---

# Publish an Office Theme

Prerequisite refresh:

1. Install with `npm install -g @arinova-ai/cli`.
2. Run `arinova auth login` and finish browser authorization.
3. Put `--profile <profile>` on every authenticated command; there is no default profile.

## Build and inspect locally

Scaffold the project, replace the placeholder preview, and edit the manifest and entry module:

```sh
arinova theme init my-theme
cd my-theme
arinova theme dev
```

The dev command binds a loopback development server on port 3100 by default and prints the address to open. It serves the real bridge with mock agent state. Stop it before building if another process needs the port.

```sh
arinova theme build
```

Build creates a flat `my-theme.zip`. It packages the declared entry as root `theme.js`, includes supported root assets, skips subdirectories, and excludes `theme.json` from the ZIP because the manifest is uploaded separately. Treat a skipped-directory warning as a broken asset layout, not a harmless warning.

## Upload, review, and publish

```sh
arinova --profile <profile> theme upload theme.json my-theme.zip
arinova --profile <profile> theme info my-theme
arinova --profile <profile> theme publish my-theme
```

Upload creates or updates a draft and runs safety scanning. Publish succeeds only after `review_status` is `approved`; an unapproved theme returns `THEME_REVIEW_REQUIRED`.

Important: every `upload` or `update` sets `published` back to false and reruns review. Updating a live theme therefore removes it from the storefront until approval and another explicit `theme publish`. Plan that interval and verify the final published state.

A high-risk upload returns `THEME_SAFETY_REVIEW_REQUIRED` and enters administrative review. There is no creator self-service rescan or unlock for a blocked theme. Correct the content when appropriate, upload a new revision, or wait for an administrator's decision; do not loop publish attempts.

Read [Theme packaging and runtime rules](references/theme.md) for manifest validation, CSP-safe authoring, bundle limits, lifecycle, ownership, and refunds.
