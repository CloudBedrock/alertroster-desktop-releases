# Install on macOS

**Requires** macOS 13 (Ventura) or later on a Mac with Apple silicon (M1 or newer).
Macs with Intel processors are not supported.

## Install

1. Download [AlertRoster-macOS.dmg](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-macOS.dmg).
2. Open it. It contains one item, **AlertRoster.pkg**. Double-click it and follow the
   installer.

   Don't copy anything to Applications by hand. The disk image has no app to drag, and
   the installer does something a copied app can't: it sets up the receiver service to
   start at login.
3. When the installer finishes, open **AlertRoster** from Applications or Launchpad.

The disk image is signed by Cloud Bedrock, LLC and notarized by Apple, so macOS opens it
without a warning.

Then continue with [First run](first-run.md).

## What the installer sets up

- `AlertRoster.app` in `/Applications`. The receiver service is inside it.
- A login item for the user who ran the installer:
  `~/Library/LaunchAgents/com.cloudbedrock.alertroster-receiverd.plist`. It starts the
  receiver service at login and restarts it if it crashes. The installer also starts the
  service immediately, so you don't have to log out.
- The receiver service's log: `~/Library/Logs/alertroster-receiverd.log`.

The receiver service belongs to the user who installed it. Its alerts, pairings and
sign-in are that user's. If the station should run under a different user account, log
in as that user and run the installer there.

## Upgrading

Run the new `AlertRoster.pkg` over the old one. If AlertRoster is open, the installer asks
you to quit it first. Your alerts, pairings, outputs and sign-in are kept.

If this Mac was set up as a wall display with **View → Launch at startup and restart on
crash**, the installer opens AlertRoster again when it finishes. Otherwise, open it
yourself.

## Uninstalling

There is no uninstaller yet. To remove AlertRoster:

1. Open AlertRoster. If **View → Launch at startup and restart on crash** is ticked,
   untick it. Then quit AlertRoster.
2. In Terminal, remove the receiver service's login item first, then stop the service:

   ```bash
   rm ~/Library/LaunchAgents/com.cloudbedrock.alertroster-receiverd.plist
   launchctl bootout gui/$(id -u)/com.cloudbedrock.alertroster-receiverd
   ```

   The order matters. If the service is stopped first, it can start again at the next
   login.
3. Drag `/Applications/AlertRoster.app` to the Trash.

This keeps your data, so a reinstall picks up where you left off. That includes alerts,
pairings, outputs, the station's settings, and the sign-in and keys stored in your
keychain.
