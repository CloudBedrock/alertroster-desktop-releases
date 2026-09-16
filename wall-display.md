# Wall displays

There are two ways to put the board on a wall:

- **A station in kiosk mode.** The computer running AlertRoster is the wall display. It
  takes over the screen for an emergency and sounds the alarm itself. This works on macOS
  and Linux.
- **A TV or monitor with a web browser, paired to a station.** Nothing is installed on
  the screen. It shows the board and cannot acknowledge or resolve anything.

## Read this first: after a power cut

The receiver service runs as part of a logged-in user's session. It starts **when that
user logs in, not when the computer turns on**. The same is true of **Launch at startup
and restart on crash**. A station that restarts after a power cut or an update therefore
sits at the login screen, with nothing running and nothing on the wall, until somebody
logs in.

For a station that must come back without anybody at it, turn on automatic login for the
user the station runs as:

- **macOS:** System Settings → Users & Groups → *Automatically log in as*. This isn't
  available while FileVault is on.
- **Linux:** your desktop's automatic login setting, for example in GNOME's Settings →
  Users. Alternatively, run `loginctl enable-linger <user>` so the receiver service starts
  at boot. The window itself still needs a desktop session.

Also set the computer to start up again after a power failure. On most PCs this is a
setting in the BIOS or firmware. On a desktop Mac, turn on *Start up automatically after
a power failure* under **System Settings → Energy** (called **Energy Saver** on older
macOS versions).

## A station in kiosk mode (macOS and Linux)

1. Set the station up as described in [First run](first-run.md), including a test alert.
2. Choose **View → Kiosk mode** (Ctrl+Shift+K). The station switches to the wall display
   layout, goes full screen, and keeps the computer from sleeping or starting its
   screensaver. It also locks the layout and hides the pointer when the mouse is idle.
   Kiosk mode stays on when AlertRoster restarts.
3. Choose **View → Launch at startup and restart on crash**. AlertRoster then opens in
   kiosk mode at login, and opens again if it stops unexpectedly. Quitting it normally
   does not reopen it.

If the station can't stop the computer from sleeping, it tells you when you turn kiosk
mode on: *Kiosk mode is on, but this machine may still sleep or run its screensaver.*
Change the computer's own power settings before relying on it.

To stop using a station as a wall display, untick **Launch at startup and restart on
crash** and **Kiosk mode** in the View menu.

## A TV or monitor with a web browser

Any screen that can open a web page can show a station's board: a smart TV's browser, a
spare laptop, or a stick computer behind a monitor.

On the station:

1. Choose **Service → Pairing…**
2. Tick **Accept sources from the LAN (off: this machine only)**, so the screen can reach
   the station over your network.
3. Choose **Open pairing window**, and keep the eight-digit code on screen. The window
   stays open for five minutes.

On the screen:

1. Open `http://<station>:4747/display` in its browser. Replace `<station>` with the
   station computer's IP address on your network, for example
   `http://192.168.1.20:4747/display`.
2. Under **Pair this display**, enter the eight digits, and where this screen is (for
   example *Front office*). Then choose **Pair**.

The screen now shows the live board and keeps its pairing, so it reconnects by itself
after being switched off. On the station, it appears under **Service → Pairing… →
Paired** as allowed to *see everything, acknowledge nothing*. Choose **Revoke** there to
remove it.

If the station stops answering, the screen says so: the board reads *NOT CONNECTED* and
the time of the last update is marked as stale. A frozen board never looks current.

The connection between the screen and the station is not encrypted. Pair screens only on
a network you control.
