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

## Running API Tester collections

`alertroster run` sends every request in an API Tester project, or in one folder of it, in
order, and checks each response with the request's assertions and test scripts. It is for
CI jobs and scheduled checks, and it doesn't need the station to be running.

```
alertroster run ~/api/MyProject --folder "Users" --env Servers=Staging \
    --junit results.xml --html report.html
```

| Option | |
|---|---|
| `--folder <name>` | Run one folder. A path such as `Users/Admin` picks a folder inside another. |
| `--tag <tag>` | Run only the requests carrying this tag, in their usual order. Repeat it, or separate tags with commas, for several: a request with any of them runs. With `--folder`, only that folder's; with `--request`, only those of the named ones that carry it. |
| `--env <group>=<environment>` | Use that environment. Without it, each environment group uses its first. |
| `--iterations <n>` | Run the requests this many times. |
| `--data <file>` | One iteration per row of a CSV file (with a header row) or a JSON array of objects. Each column fills `{{column}}` and `pm.iterationData`. |
| `--delay <ms>`, `--concurrency <n>` | Wait between requests, or send several at once. By default they go one at a time, in order. |
| `--var <name>=<value>` | A value above every other. This is how a CI job passes a secret. |
| `--allow-scripts` | Run the project's pre-request and test scripts. Without it they are skipped, unless this computer already allowed them for this project in this folder. Assertions run either way. |
| `--allow-shell` | Let `{{$shell(…)}}` and `{{$file(…)}}` values run. |
| `--allow-files` | Let requests send files from this computer (a file body, or a file in a multipart form). Without it, such a request isn't sent. |
| `--no-keychain` | Don't use this computer's keychain. Secrets then come from `--var`. |
| `--junit <file>`, `--html <file>`, `--json-report <file>` | Write a report. |
| `--json` | Print the whole run as JSON instead of a line per request. |
| `--bail` | Stop at the first failure. |
| `--alert-on <triggers>` | Raise an AlertRoster alert when the run needs somebody: `finished`, `failed` (it ended with a failure), `test` (a test failed, at once) or `unreachable` (an endpoint didn't answer, at once), separated by commas. |
| `--alert-after <minutes>` | Raise an alert if the run is still going after this long. |
| `--alert-urgency high\|low` | The alert's urgency. High unless you say otherwise. |
| `--port <port>`, `--token-file <path>` | Where the receiver service is, for the alert, when it isn't the usual one. |

An alert goes through this computer's receiver service, like **Service → Raise Test
Alert**. It reaches the board, and from there wherever this station sends its alerts. A run
raises at most one alert, for the first trigger that happens. The account running the job
needs to be able to reach the service, which on Linux means being in the station group.
`alertroster run` never starts the service itself.

With AlertRoster running, `alertroster api` drives its API Tester: `alertroster api status`,
`requests`, `send <id>`, `run --folder <id>`, `result <id>`, `environments`, `variables`,
`set-env <group>=<environment>`, `set-var <name>=<value>` and `events`. Secrets are always
shown as dots. To set a secret, give the name alone and pipe the value in
(`printf '%s' "$TOKEN" | alertroster api set-var apiKey`), so it doesn't show up in other
programs' view of the command line or in your shell history. `alertroster api --help` has the rest. `send` and `run` also work with
AlertRoster closed, given `--project <folder>`. `alertroster api call` takes any command
of the API Tester's control API as JSON and prints the answer as JSON; give it `-` to read
the JSON from standard input.

**On Windows**, a PowerShell module wraps all of this. Load it with

```powershell
Import-Module "C:\Program Files\AlertRoster\PowerShell\AlertRoster"
```

(if PowerShell says running scripts is disabled on this system, run
`Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` once first; it allows scripts
installed on this computer, such as this module) and use `Get-AlertRosterRequest`, `Send-AlertRosterRequest`, `Start-AlertRosterRun -Wait`,
`Get-AlertRosterResult`, `Get-AlertRosterEnvironment`, `Set-AlertRosterEnvironment`,
`Get-AlertRosterVariable`, `Set-AlertRosterVariable` (it takes a SecureString for a
secret), `Watch-AlertRosterEvent` and `Invoke-AlertRosterApi` for anything else. Each
returns objects, and a refusal is an error you can catch, its id naming the reason
(`AlertRoster.2002` for no such request). `Get-Help Send-AlertRosterRequest` describes each.

**On Linux**, the same commands are on the D-Bus session bus, at service
`com.cloudbedrock.AlertRoster`, object `/ApiTester`. `Call` takes a command as JSON and
returns the answer as JSON, and the `Event` signal carries each event:

```bash
busctl --user call com.cloudbedrock.AlertRoster /ApiTester \
    com.cloudbedrock.AlertRoster.ApiTester Call s '{"command":"list_requests"}'
```

A send or a run with `"wait": true` answers when it finishes, which can be longer than a
D-Bus client waits by default (25 seconds): give `busctl` a `--timeout` (or `gdbus call`
its own) long enough for it.

On both, **Request → Notify When a Run Finishes** puts a desktop notification up when a
run finishes while the API Tester is behind other windows. Clicking it brings the API
Tester back.

`alertroster mock <project>` serves the project's requests as a mock server on this
computer, each answering with its mock response or its saved response, and prints the
address. `--folder` serves one folder, `--port` picks the port, `--network` lets other
computers ask it too, and `--for <seconds>` stops it on its own; otherwise it runs until
you stop it.

`alertroster intercept <project>` runs the API Tester's interceptor for the project: a
proxy on this computer applying the project's rules. Set the address it prints as your
browser's or app's HTTP and HTTPS proxy. It prints the certificate authority it uses and
each rule. A rule that opens a host, or sends requests somewhere, that you haven't approved
on this computer stays off, and it lists what needs approving: start the interceptor once
from **Request → Interceptor…** to approve it. It never asks, and never makes, trusts or
deletes the certificate authority. `--port` picks the port, `--for <seconds>` stops it on
its own, and `--no-keychain` runs without the certificate authority, so HTTPS passes
through untouched. Then it prints a line for every request it sees.

`alertroster docs <project> --out <folder>` writes the project's API reference into the
folder: `index.html`, one page that opens in any browser, and the same as `README.md`.
`--folder` documents one folder, and `--with-saved-responses` adds the responses you pinned
as examples. They are real answers from the server, so read them before publishing.
Credentials are never written; the places one was left out are listed. It is the API
Tester's **File → Export → API Documentation…**.

`run` has exit statuses of its own:

| Exit status | Meaning |
|---|---|
| 0 | Every request was sent, and every check passed |
| 20 | The run finished, and something failed |
| 21 | The run couldn't start: the project, the folder, an environment or the data file was wrong |
| 22 | An alert was due and the receiver service didn't take it: nobody was told |
| 64 | The command was typed wrong |
