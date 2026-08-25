# Theme packaging and runtime rules

## Manifest and package

`theme.json` carries the stable theme ID, display name, version, entry, preview, and optional creator, description, tags, price, license, renderer, zone, and agent metadata.

Core validation:

- `id` is a globally unique, permanent lowercase slug no longer than 100 characters.
- `name` is 1–100 characters.
- `version` is strict `X.Y.Z` semantic versioning. Prerelease and build suffixes are not accepted.
- `entry` is a safe JavaScript filename at the project root. The runtime loads `theme.js`, and build renames another declared root entry to that filename.
- `preview` is a safe root image filename included in the bundle.
- `price` is a non-negative integer; zero or omission is free.
- `license` is `standard` or `exclusive`.

The ZIP is flat. Subdirectories and symlinks are not publishable assets. The build contains `theme.js`, the preview, and supported root assets; `theme.json` is sent as the separate multipart manifest. Current ceilings are 256 KiB for the manifest, 200 MiB for the bundle, 10 MiB per image, and 5 MiB per audio file.

Supported bundle types include common images, 3D models, audio, JSON, JavaScript, and CSS. Font files are not accepted bundle extensions. Although `theme build` packages root `.html` files, the server rejects the entire bundle when it contains HTML; remove them before upload.

## Runtime and CSP

The runtime calls only the default export's `init(sdk, container)`. Subscribe with `sdk.onResize`; exported resize or destroy hooks are not invoked.

The iframe uses strict CSP:

- Do not author inline style attributes or author style blocks. Apply styles through the CSS object model or a constructable stylesheet.
- Load bundled images and JSON through SDK asset helpers. The asset namespace is flat.
- Cross-origin fetches and remote images are blocked.
- Custom font files cannot be shipped; use a system font stack.
- Keep listeners and subscriptions bounded while mounted.

## Review lifecycle

An upload always stores a draft, sets publication false, and scans listing metadata plus JavaScript and CSS. A clean scan records approval. A high-risk scan queues manual review and returns `THEME_SAFETY_REVIEW_REQUIRED`.

`theme publish <id>` requires approval and otherwise returns `THEME_REVIEW_REQUIRED`. There is no creator rescan endpoint for blocked themes. Every later upload or update unpublishes the current listing and starts review again, so verify both approval and publication after an update.

The first creator to upload a theme ID owns it; another creator receives forbidden rather than taking over the ID.

## Marketplace behavior

Themes may be free or priced in points. Buyers have a one-hour refund window. A valid refund reverses the corresponding creator share, and that buyer cannot repurchase the same theme. Explain this before a creator chooses pricing or an exclusive license.
