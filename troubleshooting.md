# Troubleshooting

The bottom of a station's window always says whether it is connected to the receiver
service. Start with what it says there.

## What the bottom of the window means

| It says | What it means | What to do |
|---|---|---|
| **● Receiver service connected** | Everything is working. | Nothing. |
| **● Looking for the receiver service…**, **● Starting the receiver service…** or **● Connecting to the receiver service…** | The station is starting up, or the receiver service is. | Wait a few seconds. After ten seconds without an answer, the message changes to one of the rows below. |
| **● RECEIVER SERVICE LOST — reconnecting** | The receiver service was connected and stopped answering. The station keeps trying to reconnect. | If it doesn't reconnect within a few seconds, restart the receiver service (below). |
| **● NO RECEIVER SERVICE — nothing answers on 127.0.0.1:4747** | Nothing is running, and the station couldn't start it. | Restart the receiver service (below). If that doesn't help, reinstall. |
| **● NO RECEIVER SERVICE — alertroster-receiverd is not installed next to this application or on PATH.** | The receiver service is missing from this installation. | Reinstall with the installer for your platform. Don't copy the app on its own. |
| **● RECEIVER SERVICE UNAVAILABLE — the token this station holds is not one it issued** | Something is answering, but it is a different copy of the receiver service from the one this station last talked to. This usually means two copies are running. | Restart the receiver service (below). |
| **● RECEIVER SERVICE UNAVAILABLE — the receiver service answers but will not accept this station's socket** | The receiver service is running but refuses this station's connection. | Restart the receiver service (below). |
| **● RECEIVER SERVICE UNAVAILABLE — something other than the receiver service is answering on …** | Another program is using port 4747. | Quit that program, or contact support. |

While the station isn't connected, a banner across the top says so. The time of the last
data it received is shown as *STALE — last data …*. Anything on the board then is what
was true at that time, and new alerts will not appear.

## Restart the receiver service

1. Quit AlertRoster.
2. Stop every copy of the receiver service:

   - **macOS**, in Terminal: `pkill -x alertroster-receiverd`
   - **Linux**, in a terminal:

     ```bash
     systemctl --user stop alertroster-receiverd
     pkill -f '^[^ ]*/alertroster-receiverd$'
     ```

   - **Windows:** open Task Manager and choose **End task** on every
     **alertroster-receiverd.exe**.

3. Open AlertRoster again. When no receiver service is running, it starts one: through
   the login item on macOS and the user service on Linux.

## Ask the receiver service directly

`alertroster-receiverd --status` prints one line describing the running service: its
version, how many alerts are open, whether it accepts connections from your network, and
what is paired. It exits with an error if no receiver service is running for your user.

```bash
alertroster-receiverd --status                                      # Linux
/Applications/AlertRoster.app/Contents/MacOS/alertroster-receiverd --status   # macOS
```

`alertroster-receiverd --version` prints the version it was built as.

## Logs

- **macOS:** `~/Library/Logs/alertroster-receiverd.log`
- **Linux:** `journalctl --user -u alertroster-receiverd`
- **Windows:** the receiver service doesn't write a log file yet.

## Other problems

**Service → Account… says *This machine cannot store a sign-in securely.* or *The secret
store could not be read.*** The station refuses to keep a sign-in anywhere unprotected. On
Windows this is expected in this release. On Linux, the receiver service needs a keyring
service such as GNOME Keyring or KWallet running in your session. Install and unlock one,
then log out and back in.

**The sign-in email doesn't arrive.** Check the address and your spam folder. For privacy,
the station shows the same message whether or not the address has an account. Codes
expire after 10 minutes, so request a new one if it has been longer.

**A TV shows *That code was not accepted.*** The pairing window has closed or the code was
mistyped. Open a new pairing window on the station and enter the new code.

**A TV shows *Could not reach the station.*** Check that **Accept sources from the LAN** is
ticked under **Service → Pairing…**, that the address and port `4747` are right, and that
the station computer's firewall lets connections in on port 4747.

**A TV shows *● This display was removed from the station*.** It was revoked on the station.
Pair it again.

## Contact support

Contact us through [alertroster.com/support](https://alertroster.com/support). Include the
version from the bottom of the station's window, and, if you can, the output of
`alertroster-receiverd --status` and the log.
