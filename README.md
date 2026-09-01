# AlertRoster Desktop — downloads

The **receiver station**: the thing that takes an alert, sounds it, drives relays and wall
displays, and waits for a person to acknowledge it.

Everything else in AlertRoster raises alerts. This is what answers them, so the integrations
need one on the network to be useful at all.

> This repository holds **binaries and release notes only** — no source. It has its own tags
> and its own cadence, so downloads stay put no matter what any integration is doing.

## Download

| Platform | File | |
|---|---|---|
| **Windows** 10/11 (x64) | `AlertRoster-Setup.exe` | [Download](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-Setup.exe) |
| **macOS** 12+ (Universal) | `AlertRoster-macOS.dmg` | [Download](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-macOS.dmg) |
| **Linux** Debian/Ubuntu (x86-64) | `alertroster-desktop_amd64.deb` | [Download](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/alertroster-desktop_amd64.deb) |
| **Raspberry Pi** (arm64) | `AlertRoster-RaspberryPi-arm64.tar.gz` | [Download](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-RaspberryPi-arm64.tar.gz) |

Those links always resolve to the newest release, so they are safe to bookmark or link from
other projects. Asset **filenames are deliberately stable and carry no version** — the version
lives inside the package, where the installer reads it — so these URLs keep working release
after release. [All releases →](https://github.com/CloudBedrock/alertroster-desktop-releases/releases)

### Install

**Windows** — run the installer. It is signed, so SmartScreen should stay quiet.

**macOS** — open the `.dmg` and drag AlertRoster to Applications. Signed with a Developer ID,
so Gatekeeper should let it straight through.

**Debian / Ubuntu**

```bash
sudo apt install ./alertroster-desktop_amd64.deb
```

**Raspberry Pi** — the tarball carries the desktop app and `alertroster-receiverd`, the
headless receiver for a Pi on the wall:

```bash
tar xzf AlertRoster-RaspberryPi-arm64.tar.gz
```

## Verify your download

Each release publishes a `SHA256SUMS` file covering every artifact.

```bash
curl -LO https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/SHA256SUMS
sha256sum -c SHA256SUMS --ignore-missing
```

### What is signed, and what that means

| Artifact | Signing |
|---|---|
| `AlertRoster-Setup.exe` | Authenticode, **Cloud Bedrock, LLC** (SSL.com), timestamped |
| `AlertRoster-macOS.dmg` | Apple **Developer ID Application: Cloud Bedrock, LLC** |
| `.deb` and `.tar.gz` | Not individually signed — verify with `SHA256SUMS` |

The Linux artifacts are deliberately unsigned rather than accidentally so. Linux has no
per-binary signature check at install or run time: Debian's trust model signs the *repository*
metadata, not the package, and `debsig-verify` ships with no policies enabled, so a signature
inside a standalone `.deb` would be verified by nobody. Checksums are the convention for
one-off downloads, which is what these are.

## After you install it

Turn on **Accept sources from the LAN** under *Service → Pairing* so the integrations can
reach the station.

| | |
|---|---|
| **Home Assistant** | [alertroster-hacs](https://github.com/CloudBedrock/alertroster-hacs) — raise alerts from automations, react when nobody answers |
| **Omarchy** | [omarchy-alertroster](https://github.com/CloudBedrock/omarchy-alertroster) — page yourself from your desktop |
| **Asterisk** | [alertroster-ari](https://github.com/CloudBedrock/alertroster-ari) — phone the roster until someone acknowledges |

The station works standalone on your LAN. Sign in at [alertroster.com](https://alertroster.com)
and it also escalates off-site — phones, roster, on-site beacons.

## Issues

Bug reports go to the project you are using it with, or to
[alertroster.com](https://alertroster.com). Please include the version from the release you
downloaded — the tag maps to an exact build.
