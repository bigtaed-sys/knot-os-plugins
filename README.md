# KnotOS plugin store

The catalog of installable plugins for [KnotOS](https://github.com/bigtaed-sys/knot-os).

`knotd` fetches [`store.json`](store.json) (raw) and renders it on the
**Plugins** page. Each entry points at a downloadable plugin package
(a `.zip`) and an optional detached Ed25519 signature.

## Trust model

- A package whose `.sig` verifies against the **firmware release key**
  is shown as **official** and installs directly.
- Anything else is **third-party**: knotd installs it only after the
  operator explicitly confirms ("runs as a process on your router").
- The `official` flag in the catalog is a *display hint only* — trust
  is always decided by verifying the signature at install time, never
  by this flag.

## Catalog format (`store.json`)

```json
{
  "plugins": [
    {
      "id": "example-hello",
      "name": "Hello Plugin",
      "description": "One-line summary.",
      "version": "0.2.0",
      "author": "KnotOS",
      "official": true,
      "url": "https://.../example-hello-linux-arm64.zip",
      "sig_url": "https://.../example-hello-linux-arm64.zip.sig",
      "permissions": ["status:read", "devices:read", "events:read"]
    }
  ]
}
```

## Adding a plugin

1. Build a plugin per the [plugin API](https://github.com/bigtaed-sys/knot-os/blob/main/docs/plugin-api.md):
   a directory with `plugin.yaml` + an arm64 executable, zipped with the
   manifest at the root.
2. Host the `.zip` somewhere stable (a GitHub release works well) and,
   for official plugins, sign it with the release key (`.zip.sig`).
3. Add an entry to `store.json` and open a PR.

`knotd` points at this repo by default; override with
`knotd -plugins-index <url>`.
