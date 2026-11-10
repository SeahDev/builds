# Sidestash builds

GitHub Actions workflows that build sideloadable IPAs for
[Sidestash](https://github.com/SeahDev/sidestash). Each workflow clones the
tweak's original repo at the version you ask for, builds it, and uploads the
IPA as a run artifact. There are no forks to keep in sync: a new commit or
release upstream is picked up by the next build.

| Workflow | App | Builds from |
|---|---|---|
| `rain.yml` | Discord | [ra1ncord/RainTweak](https://github.com/ra1ncord/RainTweak) |
| `sparkle.yml` | Instagram | [efibalogh/sparkle-ig](https://github.com/efibalogh/sparkle-ig) |
| `neofreebird.yml` | X | [NeoFreeBird/app](https://github.com/NeoFreeBird/app) |
| `ytkace.yml` | YouTube | [itzzace/ytkace](https://github.com/itzzace/ytkace) |
| `infuseplus.yml` | Infuse | [dayanch96/InfusePlus](https://github.com/dayanch96/InfusePlus) releases |

## Inputs every workflow takes

- **`ipa_url`**: a direct link to the decrypted app. Sidestash fills this in
  when you upload one.
- **`ref`**: the upstream tag, branch or commit to build. Blank builds the
  default branch (InfusePlus: the latest release). Sidestash fills in the new
  release's tag when you start an update.

Some workflows take a few more options (FLEX, bundle ID, icon pack); they
appear in Sidestash's build form.

## What's different from the upstream workflows

- **No releases, no pushes.** The workflows only read code and upload an
  artifact, so they run with a read-only token and can't fail on a release step.
- **Links stay out of the logs.** `mask-inputs` hides every link passed in,
  including the secret part of Sidestash upload links, and no run title shows one.
- **One place to pick the Mac.** Mac jobs run on `xcode-27` (GitHub's macOS 27
  image with Xcode 27). To switch every workflow at once, add a repository
  variable `MAC_RUNNER` (Settings → Secrets and variables → Actions →
  Variables), for example `macos-15`.
- **Rain** builds the tweak and the IPA in one job, so it waits for one Mac
  instead of two. **YTKACE** builds its tweak on Ubuntu 24.04 (26.04 renamed
  `libxml2`, which breaks the Theos installer) and only builds the rootless
  package Sidestash needs.

## When a build breaks

These are copies of the upstream build steps, so if a developer changes how
their app is built (a new dependency, a pinned Theos), the copy here needs the
same change. Compare with the upstream workflow first:

- Rain: `.github/workflows/deploy.yml`
- Sparkle: `.github/workflows/buildapp.yml` and `build.sh`
- NeoFreeBird: `.github/workflows/build.yml`
- YTKACE: `.github/workflows/build-deb.yml` and `build-ipa.yml`
- InfusePlus: the asset name `*_iphoneos-arm.deb` in its releases

## Shared steps

- `.github/actions/mask-inputs`: hides links from the log.
- `.github/actions/fetch-ipa`: downloads the IPA, checks it's really an IPA,
  and reports the app's version.
- `.github/actions/setup-tools`: Homebrew `make`, `ldid`, `dpkg` and cyan.
