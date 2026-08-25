# Concepts and command conventions

## Profiles and identity

`arinova auth login` creates a user profile after browser authorization. Its name is deterministic: lowercase the returned username and replace each run of whitespace with `-`. The login command does not accept a custom profile name.

`arinova auth login` listens on port 9876 by default. If that callback port is already in use, choose another with `-p <port>` or `--port <port>`.

Bot tokens can be stored under an explicitly named profile with `arinova --profile <profile> auth set-token <your-api-key>`. Prefer browser-created user profiles for creator publishing because final ownership and scope checks still happen on the server.

Resolution has no implicit default:

1. An explicit `--token` wins but is visible in the local process list and is not persisted.
2. `--profile <profile>` selects a stored profile.
3. `ARINOVA_PROFILE` selects a stored profile when the flag is absent.
4. With none of these, authenticated commands fail with `CONFIG_ERROR`.

## Endpoint resolution

For an individual command, `--api-url` is the most explicit endpoint. Otherwise the CLI considers `ARINOVA_ENDPOINT`, the configured endpoint, and finally package-version detection. Stable versions choose production; versions containing `-staging` choose staging.

Endpoint overrides must be absolute HTTPS origins. Loopback HTTP is accepted only for local development, but publishing guidance should use the endpoint chosen by the creator or environment rather than inventing one.

## Side-effect gate

In an interactive terminal, Commander can run a remote mutation normally. In a non-interactive process, an unknown or mutating leaf requires `--yes`; observation-only commands and local `init` or `build` leaves do not. Treat this as a safety gate against accidental automation, not as authority to make a change.

## Machine-readable operation

Place global options before the resource command for consistency:

```sh
arinova --profile <profile> --json <resource> <operation>
```

Normal JSON output is one value. Streaming output is newline-delimited JSON. Errors retain stable codes in the JSON error envelope, so automation should branch on the code rather than English message text.

## IDs are scoped

- Profile names select local credentials.
- OAuth Client IDs are stable lowercase identifiers chosen when an app is created.
- Managed Space resource IDs and version IDs are server UUIDs.
- Theme IDs are globally unique author-owned slugs.
- Painter album IDs and sticker pack IDs are independent server UUIDs.

Never copy an ID into a field merely because the strings look similar.
