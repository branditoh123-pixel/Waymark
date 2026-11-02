# Waymark slim build — provenance

## Source of truth

- Upstream: CoMaps, `https://codeberg.org/comaps/comaps`, commit
  `27ea5a9b92b266abe6d1b690e02ad5c89bf95217` ("[docs] Improve map
  styles documentation", 2026-10-28).
- Full local fork: `~/workspace/waymark/`, branch `main`, HEAD
  `d80d2b27742a72161b9ddf8cacac781bf6529bae` ("Add Waymark cloud build
  workflow (GitHub Actions, debug only, no secrets)"), on top of
  `ae993ac` and Waymark Phase 1 commit `3409516`.
- This slim directory: `~/workspace/waymark-slim/` (plain files, no
  `.git` inside it). Prepared 2026-11-02, not pushed, not run.

## Patch generation

`waymark.patch` (44,414 bytes, 18 `diff --git` headers, pure UTF-8,
no "Binary files differ" lines) was generated with:

```text
git -C ~/workspace/waymark diff 27ea5a9b92b266abe6d1b690e02ad5c89bf95217..d80d2b2 -- <18 text paths>
```

The 18 text paths covered are: `android/app/build.gradle.kts`,
`android/app/src/main/java/app/organicmaps/widget/placepage/sections/PlacePageWikipediaFragment.java`,
the four `ic_launcher_foreground*.xml` and four `ic_logo*.xml`
drawables, the two `about.xml` layouts, `values/colors.xml`,
`values/donottranslate.xml`, `values/strings.xml`,
`android/sdk/src/main/cpp/app/organicmaps/sdk/editor/Editor.cpp`,
`android/settings.gradle.kts`, and `waymark-icon.svg`.

Excluded from the patch: `FORK_NOTES.md`, `CLOUD_BUILD.md`, and
`.github/workflows/waymark-cloud-build.yaml` (docs for the full fork,
not needed to build), and the five binary launcher PNGs (see below).

Note on counts: the full diff `27ea5a9..d80d2b2` is 26 files — the
18 text paths above, plus the 5 PNGs, plus the 3 excluded docs/workflow
files. Earlier briefing wording said "22 code/resource paths"; the
verified count is 23 code/resource paths (18 text + 5 PNGs).

## Binary icons

The five `ic_launcher.png` files under
`android/app/src/main/res/mipmap-*/` at HEAD were base64-encoded
(single line, `base64 -w0`) into `binaries/mipmap-*/ic_launcher.png.b64`.
`binaries/MANIFEST.txt` lists each target path, its base64 file, its
sha256 at HEAD, and its byte size:

- mipmap-hdpi: 3270 bytes, sha256 `373b8e6d5bb56c09008bbfb933182d9be0c28bf586f6370f368b3291d1f8c8af`
- mipmap-mdpi: 2150 bytes, sha256 `3fc040adb8e89cd74d3aa4ee7d61e8bb150b3beb2990f714fd73f7bce030cc92`
- mipmap-xhdpi: 4340 bytes, sha256 `44f1171b9341e339530fadf71cd35bfb95a5fe629d0830afda9dae69607d6f32`
- mipmap-xxhdpi: 6412 bytes, sha256 `cf0ee7b542c6ad7790e8f79bab4a6e4ff4693a5dc72901ea25910aeca4076825`
- mipmap-xxxhdpi: 8797 bytes, sha256 `ad93de9ad2eaec9bf958b8eb05b7cfb03f7bb6a5569b2e2932f0ee1b874fff61`

Decoding was verified locally: each decoded file hashes to the HEAD
sha256 above. The workflow re-checks every hash and fails on mismatch.

## Workflow

`.github/workflows/waymark-slim-build.yaml`, `workflow_dispatch` only,
single job on `ubuntu-latest`, debug only, no secrets, no signing, no
ccache. It reuses the apt list, `JAVA_HOME`, and `SKIP_MAP_DOWNLOAD`
from the full-fork workflow, fetches CoMaps at the pinned commit with
`git fetch --depth 1 <codeberg-url> <commit>` + `checkout FETCH_HEAD`,
checks out submodules recursively, runs `git apply --check` then
`git apply` for the patch, restores and hash-checks the icons, runs
`./configure.sh`, builds `./gradlew -Parm64 assembleFdroidDebug`, and
uploads `source/android/app/build/outputs/apk/**/Waymark-*.apk` as
`waymark-apk`.

## Simulation (patch fidelity only)

Before reporting, the patch + icons were applied to a clean extract of
the pinned upstream commit in `/tmp` and compared against HEAD for all
23 code/resource paths. See the preparation report for the result.
Gradle was NOT run here — the local sandbox daemon is broken — so the
simulation proves patch fidelity only. It does not prove the build works.

## Honest status

The cloud run is unrun. No APK is claimed until a `waymark-apk`
artifact exists on a green GitHub Actions run. The full fork's open
items are unchanged: 66 locale files still say CoMaps, map downloads
still point at the CoMaps CDN, support links still point at CoMaps,
no live traffic (upstream does not provide it), no Pixel 10a install
or Android Auto test has been done.
