# Install on Linux

There are two packages. Both contain the same station, installed under `/opt/alertroster`:

| Distribution | Requires | Download |
|---|---|---|
| Ubuntu, Debian and their derivatives | Ubuntu 24.04 or later, Debian 13 or later, x86-64 | [alertroster-desktop_amd64.deb](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/alertroster-desktop_amd64.deb) |
| Arch Linux, Omarchy | x86-64 | [alertroster-desktop-x86_64.pkg.tar.zst](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/alertroster-desktop-x86_64.pkg.tar.zst) |

The station needs a graphical desktop session. The receiver service runs as a systemd
*user* service, so it runs for whoever is logged in, and only while they are.

## Ubuntu and Debian

```bash
sudo apt install ./alertroster-desktop_amd64.deb
```

Use `apt` rather than `dpkg -i`, so that anything the package needs is installed too.

## Arch and Omarchy

```bash
sudo pacman -U alertroster-desktop-x86_64.pkg.tar.zst
```

## Start it

Open **AlertRoster** from your application menu, or run `AlertRoster` in a terminal. Then
continue with [First run](first-run.md).

The installer sets the receiver service to start at every user's login. For somebody who
is already logged in when the package is installed, it takes effect at their next login.
Until then, the first time AlertRoster opens, it starts the service itself.

## What the package sets up

- The station and the receiver service in `/opt/alertroster/bin`, with `AlertRoster` and
  `alertroster-receiverd` on your `PATH` through `/usr/bin`.
- The systemd user unit `alertroster-receiverd.service`, enabled for every user.
- An **AlertRoster** entry in the application menu.

To see the receiver service's state and log:

```bash
systemctl --user status alertroster-receiverd
journalctl --user -u alertroster-receiverd
```

## Upgrading

**Ubuntu and Debian:** install the new `.deb` the same way. The package stops a running
station, replaces it, and starts the receiver service again for the user it was running
for. It also reopens AlertRoster if it was started automatically as a wall display.

**Arch and Omarchy:** install the new package with `pacman -U`. This package doesn't yet
restart a station that is already running, so a running station keeps using the old
version until you restart it:

1. Quit AlertRoster.
2. Restart the receiver service:

   ```bash
   systemctl --user daemon-reload
   systemctl --user restart alertroster-receiverd
   ```

3. Open AlertRoster again.

Logging out and back in has the same effect.

## Uninstalling

1. Open AlertRoster. If **View → Launch at startup and restart on crash** is ticked,
   untick it. Then quit AlertRoster.
2. Stop the receiver service:

   ```bash
   systemctl --user stop alertroster-receiverd
   ```

   Removing the package disables the service for future logins, but it doesn't stop a
   service that is already running.
3. Remove the package:

   ```bash
   sudo apt remove alertroster-desktop     # Ubuntu, Debian
   sudo pacman -R alertroster-desktop      # Arch, Omarchy
   ```

Your alerts, pairings, outputs, settings and stored sign-in stay in your home directory
and your keyring, so a reinstall picks up where you left off.
