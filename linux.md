# Install on Linux

There are two packages of the full station. Both contain the same thing, installed under
`/opt/alertroster`:

| Distribution | Requires | Download |
|---|---|---|
| Ubuntu, Debian and their derivatives | Ubuntu 24.04 or later, Debian 13 or later, x86-64 | [alertroster-desktop_amd64.deb](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/alertroster-desktop_amd64.deb) |
| Arch Linux, Omarchy | x86-64 | [alertroster-desktop-x86_64.pkg.tar.zst](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest/download/alertroster-desktop-x86_64.pkg.tar.zst) |

The station needs a graphical desktop session. The receiver service does not: it runs as
the machine, starts with the machine, and keeps running with nobody logged in.

On Arch and Omarchy there is a third package,
[alertroster-receiverd-bin](#just-the-receiver-service-arch-and-omarchy), which is the
receiver service on its own, for a machine that wants one without the operator station.

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

The receiver service starts with the machine, not with your login, so a station that has
been switched off comes back to a working board with nobody touching it. The installer
starts it immediately as well, so you do not have to reboot after installing.

**Each person who uses the station needs to be in the `alertroster` group.** That is what
lets AlertRoster talk to the receiver service. The installer does not add anybody itself —
it cannot tell who runs this station from who happens to be logged in, and that group can
change the station's credentials — so it prints the command and you run it:

```bash
sudo usermod -aG alertroster <username>
```

They have to log out and back in afterwards — Linux fixes a session's groups when it
starts, so a new group does not reach a session that is already running. Until they do,
AlertRoster says *this account is not in the station group*.

## Just the receiver service (Arch and Omarchy)

Not every machine needs the operator station. A box that only has to receive alerts, ring
the relays and talk to the cloud — or one whose surface is the Omarchy status bar rather
than the station window — can install the service alone:

```bash
sudo pacman -U alertroster-receiverd-bin-x86_64.pkg.tar.zst
```

`alertroster-receiverd-bin-x86_64.pkg.tar.zst` is published beside the station's package
on the [downloads page](https://github.com/CloudBedrock/alertroster-desktop-releases/releases/latest).
If a release does not list it yet, install the full `alertroster-desktop` package instead —
it carries the same service, and the station window can simply be left unopened.

It is the same service, from the same build, with the same system unit, the same
`alertroster` group and the same `/var/lib/alertroster`, so everything below under
[What the package sets up](#what-the-package-sets-up), [Upgrading](#upgrading) and
[Uninstalling](#uninstalling) applies to it as well — minus the station window, its menu
entry, and anything reached through them.

**It cannot be installed alongside `alertroster-desktop`,** and pacman will say so. Both
carry the same service and the same unit, and two receiver services on one machine fight
over the same port and the same token: the station stops being able to reach either. If
you want the station on a machine that has this package, remove this one first.

To configure a machine with no station, attach a surface from another one — the wall
display page at `http://<this-machine>:4747/display`, or a station elsewhere on the LAN
that you pair with. `alertroster-receiverd --status` on the machine itself says whether
the service is running and what it is attached to.

## What the package sets up

- The station and the receiver service in `/opt/alertroster/bin`, with `AlertRoster` and
  `alertroster-receiverd` on your `PATH` through `/usr/bin`.
- The systemd system unit `alertroster-receiverd.service`, enabled to start at boot.
- The `alertroster` group.
- `/var/lib/alertroster`, holding this station's alerts, pairings, outputs and settings,
  and its stored credentials. The credentials are encrypted with a key belonging to this
  machine and readable only by `root`, which is what lets the service use them when
  nobody is logged in.
- An **AlertRoster** entry in the application menu.

To see the receiver service's state and log:

```bash
systemctl status alertroster-receiverd
journalctl -u alertroster-receiverd
```

## Upgrading

### Coming from 1.0.8 or earlier, read this first

Before this release the receiver service ran as *you* and kept your cloud key, your
sign-in and any AWS keys in your login keyring. It now runs as the machine, and it cannot
read your keyring — nothing can, from outside your session. So after upgrading:

- **Enter your cloud key again** under **Service → Cloud…**, sign in again under
  **Service → Account…**, and re-enter any AWS keys under **Service → AWS…** Have them to
  hand before you start.
- **Join the `alertroster` group and log out and back in.** The installer prints the
  `sudo usermod -aG alertroster <username>` line for each account it finds; run it for
  whoever uses this station. A group only reaches a session that starts after it.

Your alerts, pairings and outputs are carried over for you.

### Every upgrade

**Ubuntu and Debian:** install the new `.deb` the same way. The package stops a running
station, replaces the receiver service, starts it again, and reopens AlertRoster if it was
started automatically as a wall display.

**Arch and Omarchy:** install the new package with `pacman -U`. The receiver service is
replaced and restarted; a station you had open is yours to reopen. The same applies to
`alertroster-receiverd-bin`, which has no station to reopen.

## Uninstalling

1. Open AlertRoster. If **View → Launch at startup and restart on crash** is ticked,
   untick it. Then quit AlertRoster.
2. Remove the package:

   ```bash
   sudo apt remove alertroster-desktop          # Ubuntu, Debian
   sudo pacman -R alertroster-desktop           # Arch, Omarchy
   sudo pacman -R alertroster-receiverd-bin     # Arch, Omarchy, service only
   ```

   This stops the receiver service for you, so every relay it was driving is switched off
   rather than left on.

Your alerts, pairings, outputs, settings and stored credentials stay in
`/var/lib/alertroster`, so a reinstall picks up where you left off. To remove those too:

```bash
sudo rm -rf /var/lib/alertroster
sudo groupdel alertroster
```
