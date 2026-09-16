# AlertRoster Desktop

AlertRoster Desktop turns a computer into an **AlertRoster station**. It shows your open
incidents on a board, takes over the whole screen and sounds an alarm when one needs
somebody, and drives relays, sirens and wall displays. Every AlertRoster plan includes it.

A station is two programs, and every installer sets up both:

- **AlertRoster**, the window you look at.
- **The receiver service** (`alertroster-receiverd`), which runs in the background. It
  holds the alerts, sounds the outputs, and stays signed in to your account while the
  window is closed. The installer starts it at login.

## Download

| Platform | Requires | Download |
|---|---|---|
| **Windows** | Windows 10 or 11, 64-bit | [AlertRoster-Setup.exe](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-Setup.exe) |
| **macOS** | macOS 13 or later, Apple silicon (M1 or newer) | [AlertRoster-macOS.dmg](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-macOS.dmg) |
| **Ubuntu / Debian** | Ubuntu 24.04 or later, Debian 13 or later, x86-64 | [alertroster-desktop_amd64.deb](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/alertroster-desktop_amd64.deb) |
| **Arch / Omarchy** | x86-64 | [alertroster-desktop-x86_64.pkg.tar.zst](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/alertroster-desktop-x86_64.pkg.tar.zst) |
| **Raspberry Pi** | See the [Raspberry Pi page](raspberry-pi.md) | [AlertRoster-RaspberryPi-arm64.tar.gz](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-RaspberryPi-arm64.tar.gz) |

These links always point at the newest release. The file names never change, so the
links are safe to bookmark. What changed in each release is on the
[releases page](https://github.com/CloudBedrock/alertroster-desktop-releases/releases).

## Set up a station

1. **Install it:** follow the page for [Windows](windows.md), [macOS](macos.md) or
   [Linux](linux.md).
2. **Sign it in and check it works:** see [First run](first-run.md). This covers signing
   in to your account, raising a test alert, and connecting sirens and integrations.
3. **Put it on a wall** (optional): see [Wall displays](wall-display.md). Read this
   before relying on a station that nobody is sitting at. **The receiver service starts
   when someone logs in to the computer, not when the computer turns on.**

If something doesn't look right, see [Troubleshooting](troubleshooting.md).

## Integrations

These send alerts to a station on your network. Pair each one under **Service →
Pairing…**. See [First run](first-run.md#5-connect-integrations).

| | |
|---|---|
| **Home Assistant** | [alertroster-hacs](https://github.com/CloudBedrock/alertroster-hacs): raise alerts from automations, and react when nobody answers |
| **Omarchy** | [omarchy-alertroster](https://github.com/CloudBedrock/omarchy-alertroster): page yourself from your desktop |
| **Asterisk** | [alertroster-ari](https://github.com/CloudBedrock/alertroster-ari): phone the roster until someone acknowledges |

The [n8n node](https://github.com/CloudBedrock/n8n-nodes-alertroster) is different. It
works with your AlertRoster account over the internet, with its own credentials, so it
doesn't pair with a station.

## Check your download

Every release publishes a `SHA256SUMS` file covering each download. In the folder you
downloaded into:

```bash
curl -LO https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/SHA256SUMS
sha256sum -c SHA256SUMS --ignore-missing
```

On a Mac, use `shasum -a 256 -c SHA256SUMS --ignore-missing`. On Windows, run
`Get-FileHash AlertRoster-Setup.exe` in PowerShell and compare the result with the line for
that file in `SHA256SUMS`.

| Download | Signed by |
|---|---|
| `AlertRoster-Setup.exe` | Authenticode, **Cloud Bedrock, LLC** |
| `AlertRoster-macOS.dmg` | Apple Developer ID, **Cloud Bedrock, LLC**, notarized by Apple |
| Linux and Raspberry Pi files | Not signed individually. Check them against `SHA256SUMS`. |

## Upgrading

Download the new version and install it over the one you have. You don't need to
uninstall first, and your settings, pairings and sign-in are kept. The installer stops
the station, replaces it, and starts the receiver service again. A station that was
started automatically, such as a wall display, starts again too. A window you opened
yourself stays closed until you open it again.

The version a station is running is at the bottom of its window, for example
`v1.0.8-112`.

## Getting help

Contact us through [alertroster.com/support](https://alertroster.com/support). Include the
version from the bottom of the station's window. That number identifies the exact build
you are running.
