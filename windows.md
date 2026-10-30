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
- `alertroster://` links, for the user who ran the installer: a link to an incident from
  a note, a ticket or a chat opens it on this station, in the window you have open if
  AlertRoster is already running. A link only shows things; it never acknowledges,
  resolves or changes anything.
- A PowerShell module for the API Tester, in `C:\Program Files\AlertRoster\PowerShell`.
  The command line guide has the details.
- A sign-in startup entry, **AlertRosterReceiver**, for the user who ran the installer.
  It starts the receiver service each time that user signs in to Windows. The installer
  also starts the service immediately. You can see the entry under **Task Manager →
  Startup apps**.

Your sign-in and your cloud integration key are kept in the **Windows Credential
Manager**, under the account you are signed in to Windows as. They are encrypted with
your Windows account's own key material and stay on this computer — they don't roam to
other machines you sign in to.

The receiver service belongs to the user who installed it. Its alerts, pairings and
sign-in are that user's. If the station should run under a different user account,
sign in as that user and run the installer there.

## Upgrading

Run the new `AlertRoster-Setup.exe`. The installer closes AlertRoster and the receiver
service, replaces them, and starts the receiver service again. Open AlertRoster from the
Start menu when it finishes. Your alerts, pairings, outputs and sign-in are kept.

## Installing with the MSI

For a single PC, use `AlertRoster-Setup.exe` as above. For a silent or scripted install,
there is also an MSI package:
[AlertRoster.msi](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/AlertRoster.msi).
It installs the same files and sets up the same things, and it is signed by **Cloud
Bedrock, LLC** like the setup program.

From an administrator command prompt:

```
msiexec /i AlertRoster.msi /qn
```

and to remove it:

```
msiexec /x AlertRoster.msi /qn
```

The receiver's sign-in startup entry and the `alertroster://` link handler are set up for
**the account that runs the installer**. The MSI installs for the whole computer, so it
needs that command prompt opened with administrator rights — which only works out if
the station's own user is a Windows administrator and opens it as themselves. If they
are a standard user, the administrator sign-in you would type in becomes the account
that gets those entries; use `AlertRoster-Setup.exe` instead, which asks for
administrator permission only to copy the files.

**Not yet for Intune, Group Policy or SCCM.** Those tools run the installer as the
`SYSTEM` account, so the receiver would not start, and links would not open, for the
people who sign in to the station. That is being fixed; until then, install it as the
station's user.

Use one installer or the other on a PC, not both. They install to the same folder, and
each would remove files the other still expects. To switch from `AlertRoster-Setup.exe`
to the MSI, uninstall AlertRoster first.

A new MSI upgrades the one already installed: run it the same way. In **Installed apps**
its version shows as `1.0.<build>` rather than the release number — `1.0.230` for release
1.0.9-230 — because Windows compares only the first three parts of an MSI's version
when it decides whether something is newer.

## Running a Windows station as a wall display

**View → Kiosk mode** and **View → Launch at startup and restart on crash** both work on
Windows. See [Wall displays](wall-display.md) for how to set one up.

Two things are worth knowing before you rely on one:

- AlertRoster starts when you **sign in**, not when the machine boots. For a display
  that has to come back on its own after a power cut, also turn on automatic sign-in for
  that account in Windows.
- If the station stops unexpectedly, it opens again within a few seconds. Quitting it
  normally does not reopen it. While it is running as a wall display you will see two
  **AlertRoster** entries in Task Manager: one is the station, the other is what reopens
  it.

The startup entry is a scheduled task called **AlertRoster**, which you can see in Task
Scheduler under **Task Scheduler Library**. Unticking **Launch at startup and restart on
crash** removes it, and so does deleting it there.

## Uninstalling

Open **Settings → Apps → Installed apps**, find **AlertRoster** and choose **Uninstall**.
The uninstaller stops AlertRoster and the receiver service, and removes the Start menu
shortcut, the receiver service's startup entry and the `alertroster://` link handler.

If the station was a wall display, untick **View → Launch at startup and restart on
crash** before uninstalling. The uninstaller does not remove that entry yet, and one left
behind tries to start AlertRoster at each sign-in until you delete the **AlertRoster** task
in Task Scheduler.
