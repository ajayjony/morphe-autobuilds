# Morphe AutoBuilds

Automated, non-root [Morphe](https://github.com/MorpheApp) builds of **YouTube**, **YouTube Music** and **Reddit**.

Builds run automatically every day and publish signed APKs to [**Releases**](../../releases).

Built on [j-hc/revanced-magisk-module](https://github.com/j-hc/revanced-magisk-module), configured for the Morphe patch set.

## Downloads

Grab the latest APKs from the [releases page](../../releases/latest).

| App | Architecture | Notes |
|---|---|---|
| YouTube | universal (`all`) | needs MicroG-RE |
| YouTube Music | `arm64-v8a` | needs MicroG-RE |
| Reddit | universal (`all`) | — |

> **MicroG required.** Non-root YouTube and YouTube Music will not log in or play without
> [MicroG-RE](https://github.com/MorpheApp/MicroG-RE/releases/latest). Install it once, then the APKs.

### In-app updates

`enable-update-checks` is on, so each patched app checks **this repo's** releases and
offers an in-app update when a newer build lands. No store, no manual checking.

## How it works

| Workflow | Trigger | What it does |
|---|---|---|
| [`ci.yml`](.github/workflows/ci.yml) | daily at 16:00 UTC (21:30 IST) + manual | Checks for new app/patch versions; builds only if something changed |
| [`build.yml`](.github/workflows/build.yml) | called by CI + manual | Patches, signs and publishes a release |

Release tags are incrementing integers (`1`, `2`, `3`, …). Build notes land in each release body
and in [`build.md`](../../blob/update/build.md) on the `update` branch.

Stock APKs come from the [archive.org mirror](https://archive.org/download/jhc-apks/apks/),
with APKMirror as automatic fallback.

## Configuration

Everything lives in [`config.toml`](./config.toml). Full option reference: [`CONFIG.md`](./CONFIG.md).

Add an app:

```toml
[Some-App]
build-mode = "apk"
apkmirror-dlurl = "https://www.apkmirror.com/apk/org/app"
```

Exclude patches from an app:

```toml
[YouTube]
excluded-patches = """\
  'Custom branding' \
  'Theme' \
  """
```

Then run the [build workflow](../../actions/workflows/build.yml) manually, or wait for the nightly run.

### Getting the exact patch names

Patch names must match exactly. Run the [**List patches**](../../actions/workflows/list-patches.yml)
workflow (Actions → List patches → Run workflow) and download the `morphe-patch-lists`
artifact. It contains, for each app:

- `names-<app>.txt` — every patch name, one per line, ready to paste into `config.toml`
- `patches-<app>.txt` — full detail: descriptions, default on/off, and every patch option with its possible values

Current counts for Morphe patches v2.1.0: **YouTube 118**, **YouTube Music 56**, **Reddit 36**.

## Signing

APKs are signed with the committed `ks.keystore` (the public upstream key, same as
`VVispy/morphe-builder`), so builds from this repo install over existing ones
without an uninstall.

## Optional: Telegram build notifications

Set both repository secrets and every successful build gets posted:

- `TG_TOKEN` — bot token from [@BotFather](https://t.me/BotFather)
- `TG_CHAT` — chat ID or `@channelname`

Without them the step is skipped.

## Building locally

```console
git clone https://github.com/ajayjony/morphe-autobuilds --depth 1
cd morphe-autobuilds
./build.sh
```

Requires Java 21, `zip`, `curl`, `jq`. On Termux:

```console
bash <(curl -sSf https://raw.githubusercontent.com/ajayjony/morphe-autobuilds/main/build-termux.sh)
```

## Disclaimer

Build automation only — no APKs or copyrighted material are stored in this repository.
Outputs are generated in CI from third-party sources for personal use. Not affiliated with
Morphe, ReVanced, or any app publisher.
