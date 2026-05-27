# rancher-ui-bundles

Built UMD bundles of [focusnet](https://focusnet.de)'s custom Rancher UI extensions, served to the [Rancher UIPlugin](https://ranchermanager.docs.rancher.com/integrations-in-rancher/rancher-extensions) operator via `raw.githubusercontent.com`.

This repository **contains only build artefacts** — no source code. Source lives in an internal GitLab repository and is built by GitLab CI; this repo is a publish target so the Rancher UIPlugin operator can fetch the bundles anonymously.

## Available extensions

| Extension | Latest version | Purpose |
|-----------|----------------|---------|
| `cloud-services-marketplace` | see `extensions/cloud-services-marketplace/` | Lists focusnet's bookable add-on services (SUSE Observability, NeuVector, MSV Images, …) inside the Rancher UI and can import standard VM images into customer MSV clusters. |

## Layout

```
extensions/
  <extension-name>/
    <version>/                                   # un-tarred UMD bundle (UIPlugin endpoint)
      <extension-name>-<version>.umd.min.js
      <extension-name>-<version>.umd.min.<n>.js  # code-split chunks
      ...
    <version>.tgz                                # same content, gzipped (UIPlugin compressedEndpoint)
```

## Consuming a bundle

Apply a `UIPlugin` resource on the Rancher management cluster pointing at the raw URLs:

```yaml
apiVersion: catalog.cattle.io/v1
kind: UIPlugin
metadata:
  name: cloud-services-marketplace
  namespace: cattle-ui-plugin-system
spec:
  plugin:
    name: cloud-services-marketplace
    version: "0.1.2"
    endpoint: "https://raw.githubusercontent.com/focusnetcloud/rancher-ui-bundles/main/extensions/cloud-services-marketplace/0.1.2"
    compressedEndpoint: "https://raw.githubusercontent.com/focusnetcloud/rancher-ui-bundles/main/extensions/cloud-services-marketplace/0.1.2.tgz"
    metadata:
      catalog.cattle.io/rancher-version: ">= 2.10.0-0"
      catalog.cattle.io/ui-extensions-version: ">= 3.0.0 < 4.0.0"
```

Rancher's UIPlugin operator caches the bundle inside the management cluster after first fetch.

## Versioning

Each version directory is immutable once published. New extension releases get a new directory; old versions stay so existing `UIPlugin` resources keep resolving.

## Updates

This repo is updated by an automated pipeline. Manual edits will be overwritten by the next CI publish. To request changes or report issues with an extension, contact `ioc@focusnet.de`.

## License

Code in this repository is published under the MIT License. See `LICENSE`.
