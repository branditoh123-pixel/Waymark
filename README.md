# Waymark — slim cloud build

Waymark is a rebranded fork of CoMaps, an offline-first open-source map
and navigation app. This repository does **not** hold the full CoMaps
source. It holds only what is needed to rebuild Waymark in the cloud:

- `waymark.patch` — the Waymark changes as a text patch (app name
  Waymark, app ID `app.waymark`, strings, colours, icons, about
  screens, and the OpenStreetMap `created_by` value "Waymark").
- `binaries/` — the five launcher icon PNGs as base64 text, with a
  manifest of their sha256 hashes, because a file upload cannot carry
  binary files directly.
- `.github/workflows/waymark-slim-build.yaml` — the build workflow.

When the workflow runs, it fetches the full CoMaps source from Codeberg
at the pinned commit
`27ea5a9b92b266abe6d1b690e02ad5c89bf95217`, applies the patch, restores
the icons and checks their hashes, then builds the F-Droid debug APK
for arm64.

Licences and attribution are preserved: Waymark is based on CoMaps and
OpenStreetMap data, under the same Apache-2.0 licence. No tracking,
analytics, or ads are added.

## How to build

On GitHub, in this repository, open the **Actions** tab, choose
**Waymark Slim Build**, and press **Run workflow**. It runs only when
you trigger it manually.

When the run finishes green, open the run, scroll to **Artifacts**,
and download `waymark-apk`. Unzip it on your phone and install the APK
inside. Android will ask you to allow unknown-app installs for the
browser or Files app — allow it for that install only. This is a
debug-signed APK, not a store release.

## How to test on a Pixel 10a

- The app name shows as "Waymark" (debug builds show "Waymark Debug").
- The Waymark icon (amber pin on dark navy) shows in the launcher.
- Settings / app info shows package `app.waymark.fdroid.debug`.
- Download an offline map for your area and confirm it downloads.
- Plan one real driving route and confirm voice guidance speaks.

No APK exists until an artifact appears on a green run. See
`SLIM_BUILD.md` for the exact provenance of the patch and icons, and
for what is still unproven.
