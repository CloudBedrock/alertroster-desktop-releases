# The command line

The macOS and Linux installers put a command called `alertroster` on the machine; on
Windows and the Raspberry Pi it is `alertroster-cli`, beside the station. It drives the station
from a terminal, a shell script, Keyboard Maestro, Alfred, BetterTouchTool, Hazel or
anything else that can run a command. It talks to this machine's receiver service, so it
works with the station's window closed.

| Platform | Where it is |
|---|---|
| macOS | `/usr/local/bin/alertroster` (the installer links it there) |
| Linux, Arch, Omarchy | `/usr/bin/alertroster` |
| Windows | `alertroster-cli.exe` in the AlertRoster install folder. Add that folder to your `PATH` to type it from anywhere. |
| Raspberry Pi | `alertroster-cli`, beside the station in the folder you unpacked |

## Commands

```sh
alertroster status                 # connected?, and what is open
alertroster alerts                 # every open alert, one per line, id first
alertroster alert <id>             # one alert in full
alertroster ack <id>               # acknowledge, recorded as you, marked (automation)
alertroster ack <id> --as "Night supervisor"
alertroster resolve <id>
alertroster silence <id>           # quiets AlertRoster's paging; relays and sirens stay on
alertroster reassign <id> "Morgan Lee"   # a responder's name, email or id
alertroster responders
alertroster checkins
alertroster check-in               # answers your armed check-in
alertroster extend                 # pushes its deadline back
alertroster link <id> --markdown   # a link to the incident that keeps working after it closes
alertroster open <id>              # opens the alert's record in the station window (Mac)
```

Add `--json` to any command for output a script can parse, for example with `jq`:

```sh
alertroster alerts --json | jq -r '.alerts[] | select(.emergency) | .title'
```

## It works like the station's buttons

- **An action finishes when the receiver service has done it.** When `alertroster ack`
  returns, the alert is acknowledged.
- **It can only do what the alert offers.** Acknowledging an alert that is already
  acknowledged is refused, with the reason.
- **A station that isn't connected says so.** If the receiver service isn't answering,
  `alertroster alerts` fails within a few seconds. It never prints an empty list that
  could be mistaken for "nothing is happening".
- **It never starts the receiver service.** If it isn't running, the command says so.

On Linux, run it as a member of the station group, the same as the station itself. See
[Install on Linux](linux.md).

## Exit status

A script can branch on the exit status without reading the text:

| Exit status | Meaning |
|---|---|
| 0 | Done |
| 1 | The station isn't connected to its receiver service |
| 2 | No open alert has that id |
| 3 | The alert doesn't offer that right now |
| 4 | Another request for that alert is still waiting for an answer |
| 5 | The receiver service or AlertRoster said no; the message says why |
| 6 | Sent, but no answer came back; it may still have happened |
| 7 | Something in the command was not right for the station, such as several check-ins armed and none named |
| 8 | No such check-in, or none is armed |
| 9 | Not possible here: the station window is not open, or `open` off a Mac |
| 10 | No responder by that name |
| 64 | The command was typed wrong |

The error number and the reason are printed too, for example
`1003: "Gate open" does not offer Acknowledge right now (it is acknowledged).`
