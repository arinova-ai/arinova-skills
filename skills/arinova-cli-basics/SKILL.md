---
name: arinova-cli-basics
description: Install, authenticate, configure, and troubleshoot @arinova-ai/cli, and choose the correct creator flow for Arinova Space, theme, Painter, sticker, publish, 上架, 發佈, or 創作者 requests. Use for CLI setup and cross-product publishing questions; use a product entry for detailed execution.
---

# Arinova Creator CLI Basics

Use the official global CLI and keep the creator in control of every remote mutation.

## Install and authenticate

Install the released package globally. Do not substitute another package runner.

```sh
npm install -g @arinova-ai/cli
arinova auth login
```

Login opens the Arinova authorization page and waits for a loopback callback. After authorization, the CLI derives the profile name from the account username: it lowercases the username and changes whitespace runs to `-`. The creator cannot choose a different name during browser login.

There is no default profile. Every authenticated command needs either `--profile <profile>` or `ARINOVA_PROFILE`. Confirm the stored identity before publishing:

```sh
arinova --profile <profile> auth whoami
arinova --profile <profile> --json space owned
```

Use `<your-api-key>` whenever a placeholder is unavoidable. Never ask the creator to paste a live credential into chat, a committed file, or a process argument.

## Select the endpoint deliberately

The released stable CLI selects production. A package version containing `-staging` selects staging. A command-level `--api-url` overrides that selection; `ARINOVA_ENDPOINT` or `arinova config set endpoint <https-api-origin>` can set an alternate endpoint for a session or configuration.

Do not infer that a profile belongs to the selected endpoint. Run `auth whoami` after changing endpoints, and stop if the identity or environment is not the intended one.

## Output and non-interactive execution

- Add `--json` when another program will consume the result. Successful commands emit one JSON value; streamed results use newline-delimited JSON.
- Remote side effects fail closed in a non-interactive process unless `--yes` is present. `--yes` is an execution acknowledgement, not permission: add it only after the creator has authorized that exact mutation and target.
- Local `init` and `build` commands remain usable without `--yes` because they do not call the service.
- Creator upload routes share a default limit of 15 uploads per minute. On HTTP 429, honor `Retry-After` and resume later instead of retrying rapidly.

## Choose the publishing kind

Ask what the creator is shipping, then route narrowly:

- A packaged web app or game with OAuth, storage, or commerce: use the `arinova-cli-space` entry.
- An Office visual runtime with `theme.js`: use the `arinova-cli-theme` entry.
- An AI image-style album: use the Painter track in `arinova-cli-painter-sticker`.
- A reusable messaging sticker collection: use the sticker track in `arinova-cli-painter-sticker`.
- An Expert listing: the CLI Expert surface is migration-only. Build an agent with a skill package and complete listing publication in the web creator flow.

Do not silently switch kinds. Space IDs, OAuth Client IDs, theme IDs, Painter album IDs, and sticker pack IDs are different resources.

## Recover safely

Read [Concepts and command conventions](references/concepts.md) when profiles, endpoints, confirmation behavior, or identity types are unclear. Read [Error recovery](references/errors.md) when a command returns a stable error code. Preserve the server code and structured `details` in any report; they often identify the exact missing field or lifecycle state.
