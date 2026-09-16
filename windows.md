# Install on Windows

**Requires** Windows 10 or 11, 64-bit.

## Install

1. Download [AlertRoster-Setup.exe](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster-Setup.exe).
2. Run it and follow the installer. Run it as the Windows user who will use the station,
   not with **Run as administrator**. The installer asks for administrator permission
   itself when it needs it.

   The installer is signed by **Cloud Bedrock, LLC**. If SmartScreen shows *Windows
   protected your PC*, choose **More info** and check that the publisher is Cloud Bedrock,
   LLC before choosing **Run anyway**.
3. On the last page, leave **Launch AlertRoster** ticked and choose **Finish**.

After that, open AlertRoster from the Start menu.

Then continue with [First run](first-run.md).

## What the installer sets up

- AlertRoster in `C:\Program Files\AlertRoster`. The receiver service,
  `alertroster-receiverd.exe`, is installed next to it.
- An **AlertRoster** shortcut in the Start menu.
- A sign-in startup entry, **AlertRosterReceiver**, for the user who ran the installer.
  It starts the receiver service each time that user signs in to Windows. The installer
  also starts the service immediately. You can see the entry under **Task Manager →
  Startup apps**.

The receiver service belongs to the user who installed it. Its alerts, pairings and
sign-in are that user's. If the station should run under a different user account,
sign in as that user and run the installer there.

## Upgrading

Run the new `AlertRoster-Setup.exe`. The installer closes AlertRoster and the receiver
service, replaces them, and starts the receiver service again. Open AlertRoster from the
Start menu when it finishes. Your alerts, pairings, outputs and sign-in are kept.

## Uninstalling

Open **Settings → Apps → Installed apps**, find **AlertRoster** and choose **Uninstall**.
The uninstaller stops AlertRoster and the receiver service, and removes the Start menu
shortcut and the startup entry.

## Not available on Windows yet

- **Signing in to your account.** This release can't store a sign-in securely on
  Windows, and the station doesn't keep one any other way. Until it can, a Windows
  station doesn't show your account's incidents (for example, missed check-ins), and
  **Service → Cloud…** can't store an integration key for off-site escalation. Local
  alerts, outputs and paired integrations all work.
- **Kiosk mode.** **View → Kiosk mode** and **View → Launch at startup and restart on
  crash** are greyed out on Windows. A wall display that must stay on without
  supervision currently needs a Mac or a Linux machine. Alternatively, you can show the
  board on a TV's web browser from any station. See [Wall displays](wall-display.md).
