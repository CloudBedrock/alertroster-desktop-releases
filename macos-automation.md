# Automate a Mac station

On a Mac, AlertRoster can be driven by other programs: **AppleScript** and **JavaScript
for Automation**, **Shortcuts**, and **`alertroster://` links**. Anything built on them
works too: Raycast, Keyboard Maestro, Alfred, a Stream Deck button that runs a shortcut,
a Focus filter, or a script run on a schedule.

A script can do anything the station's buttons do. It works the same way they do:

- **Every action goes through the receiver service**, like a button press. A script that
  acknowledges an alert gets its answer once the service has acknowledged it, not when
  the request was sent.
- **A script can only do what the alert offers.** If the Acknowledge button would be
  greyed out, a script asking to acknowledge gets an error that says why.
- **A station that isn't connected says so.** Reading the alerts while the station has
  lost its receiver service is an error, never an empty list. A script that sees no
  alerts can trust that there are none.

An acknowledgement made by a script is recorded under your login name, marked
"(automation)", so an after-action review can tell it apart from someone at the board.
A script can name somebody else instead.

The first time a program sends AlertRoster a command, macOS asks whether to allow it:
*"Script Editor" wants access to control "AlertRoster"*. Allow it once for each program
you automate from. You can change your mind later in **System Settings → Privacy &
Security → Automation**.

## AppleScript

Open **Script Editor**, choose **File → Open Dictionary…** and pick AlertRoster to see
every command and property. Some examples:

```applescript
tell application "AlertRoster"
    -- Is the board current, and is anything an emergency?
    if not connected then return "Station offline: " & service problem
    if emergency then display notification "Emergency on the board"

    -- Every open alert nobody has acknowledged yet
    get name of every alert whose status is triggered

    -- Acknowledge one, and record who did it
    acknowledge (first alert whose name contains "Freezer") recorded as "Night supervisor"

    -- Acknowledge everything low-urgency
    acknowledge (every alert whose status is triggered and urgency is low urgency)

    -- Where is the subject of an incident?
    get {location, latitude, longitude} of first alert whose duress is true

    -- Bring an alert up on screen, or open its search panel or thread
    show first alert whose emergency is true
    open search panel (first alert whose name contains "overdue")

    -- Answer your check-in
    check in

    -- Switch the layout (not while kiosk mode is on, which locks it)
    set layout to wall display
end tell
```

The commands are **acknowledge**, **resolve**, **silence**, **reassign … to**,
**approve** and **reject** (each **with note**), **show**, **open thread**, **open
search panel**, **share report**, **raise test alert**, **check in**, **extend
checkin**, **list responders** and **open record**. The station's own properties include
**connected**, **service state**, **service problem**, **last update**, **emergency**,
**selected alert**, **current title**, **current link**, **current markdown link**,
**layout**, **theme** and **kiosk mode**. Every alert has a **link** and a **markdown
link**.

Given one alert, a command returns that alert as it now stands. Given several, it acts
on each in turn, stops at the first one it can't do, and returns nothing. Read the
alerts again to see where they ended up.

### When a command can't be done

Each refusal has a number, so a script can tell them apart:

| Number | Meaning |
|---|---|
| 1001 | The station isn't connected to its receiver service, so what it shows isn't current. |
| 1002 | No open alert has that id. It may have been resolved. |
| 1003 | The alert doesn't offer that right now, for example acknowledging one that is already acknowledged. |
| 1004 | Another request for that alert is still waiting for an answer. Try again in a moment. |
| 1005 | The receiver service or AlertRoster said no. The message says why. |
| 1006 | The request was sent and no answer came back. It may still have happened, so read the alert again. |
| 1007 | Something in the command was wrong, such as an unknown layout name. |
| 1008 | No such check-in, or none is armed. |
| 1009 | The station window isn't open to do that. |
| 1010 | No responder by that name, id or email. |

```applescript
tell application "AlertRoster"
    try
        acknowledge alert id "la_00T2B3QBD97PTQN79G9BY8MRBE"
    on error message number n
        if n is 1003 then return "Already handled"
        error message number n
    end try
end tell
```

### JavaScript for Automation

The same dictionary works from JavaScript:

```javascript
const station = Application("AlertRoster");
station.alerts().filter(a => a.emergency()).map(a => a.name());
```

From Terminal, run either kind with `osascript`, or use the `alertroster` command
itself. See [The command line](command-line.md).

### The API Tester

The dictionary's **API Tester Suite** works on the project open in **Window → API
Tester**: its requests and folders, environments and variables, what each request got
back, and collection runs.

```applescript
tell application "AlertRoster"
    set p to current api project

    -- Send a request and read what came back
    set x to send (first api request of p whose name is "Health check")
    get {status code, response time, tests failed} of x

    -- Make a request in a folder, then change it
    set r to make new api request at (api folder "Smoke" of p) ¬
        with properties {name:"Version", url:"{{base}}/version"}
    set method of r to "POST"

    -- Copy one, and delete what you no longer need
    duplicate r with properties {name:"Version (staging)"}
    delete (every api request of p whose name starts with "Scratch")

    -- Switch environment, and run a folder to the end
    set active environment of api environment group "Servers" of p to "Staging"
    with timeout of 3600 seconds
        set theRun to start run (api folder "Smoke" of p) with waiting until done
    end timeout
    get {done, tests passed, tests failed} of theRun
end tell
```

**send** answers when the response is back. **start run** (AppleScript keeps the word
*run* for itself) answers at once with the run, or when it ends **with waiting until
done**; it also takes **iterations**, **concurrency**, **delay** and **stopping at
first failure**. **make** needs **at**: the project, for its top level, or a folder.
Every request has a **link** that opens it in the API Tester, and its **api exchanges**
are its history, newest first.

Scripts never see a secret. A secret variable's **value** reads as dots, and what an
exchange shows has credentials and secret values already taken out, as the request's
history does. Setting a secret variable's value puts it in this Mac's keychain, never in
the project. A script sends with the scripts and files you already allowed for the
project, and never puts a question on the screen. If a request needs something you
haven't allowed, it isn't sent, and the error says why.

The API Tester's refusals are numbered from 2001:

| Number | Meaning |
|---|---|
| 2001 | No project is open in the API Tester. |
| 2002 | The project has no such request. |
| 2003 | The project has no such folder. |
| 2004 | No environment group defines that variable. |
| 2005 | The project has no such environment. |
| 2006 | That project can't be opened. |
| 2007 | Not sent. The message says why. |
| 2008 | Something in the command was wrong, such as duplicating a folder. |
| 2010 | No such run. |
| 2011 | Nothing has been sent yet. |

Five sample scripts come with AlertRoster: send a request, run a folder, switch
environment, make a request from the URL on the clipboard, and list the requests that
are failing. They are in **AlertRoster.app/Contents/Resources/Sample Scripts/API
Tester**. Open one in Script Editor to read it or run it.

## Shortcuts

Open the **Shortcuts** app and search for AlertRoster. The actions are:

- **Get Station Status**: whether the station is connected, how many alerts are open,
  and whether any is an emergency.
- **Find Alerts**, for example *where Emergency is true*.
- **Acknowledge Alert**, **Resolve Alert**, **Silence Paging**, **Reassign Incident**,
  **Approve Request**, **Reject Request**.
- **Show Alert**, **Open Incident Thread**, **Open Search Panel**.
- **Share Responder Report**, which returns the report's link.
- **Raise Test Alert**, **Check In**, **Extend Check-in**.
- **Set Station Layout**, **Set Station Theme**, **Set Kiosk Mode**.
- **Open Incident Record**, and **Get Incident Link**, which returns a link that keeps
  working after the incident closes. With no alert chosen, it links the one in front of
  you.

A few are ready to use with no setup: *AlertRoster status*, *Acknowledge an alert in
AlertRoster*, *Check in with AlertRoster*.

### The API Tester's actions

These work on the project open in **Window → API Tester**:

- **Send API Request**, which returns the response: its status code, body, headers,
  time, and how many tests passed and failed.
- **Get Last API Response**, the newest response a request got, without sending it again.
- **Run API Requests**, for a folder or the whole project, as many times as you say. It
  waits for the run to end unless you turn **Wait Until Done** off.
- **Get API Run Result**, for a run that is still going or has finished.
- **Set API Variable** and **Switch API Environment**.
- **Open API Request**, which brings the API Tester forward with the request selected.
- **Find API Requests**, **Find API Environments** and the like, for example *API
  Requests where Folder is Smoke*.

Shortcuts sees what a script sees. Secret values read as dots. A response shows nothing
the request's history wouldn't, and that leaves out credentials and secret values.
Requests run only the scripts and files you already allowed for the project. A refusal
stops the shortcut with a sentence saying why.

### Services

With text selected in any app, the **Services** menu (right-click, or the app's menu
under its name) has two items:

- **New API Request from cURL** makes a request from a `curl` command, such as the one a
  browser's *Copy as cURL* gives you or an API's documentation shows.
- **New API Request from URL** makes a GET request from a link.

The request goes at the top of the open project, and the API Tester comes forward with
it selected. Nothing is sent until you send it. What the command does that AlertRoster
won't take from copied text is listed in the request's description: a file it would
upload, a proxy, turning off the TLS certificate check, and options a request can't
hold, such as a client certificate. Set any of those yourself if you mean them. Only
`http` and `https` addresses are taken.

## Run a shortcut when something happens

Shortcuts can't start a shortcut when another app's alert arrives, but the station can.
In **Service → Outputs…**, choose **Add…** and then **Run a shortcut on this Mac**:

- **Shortcut:** pick one of yours from the list.
- **Run when an alert is:** raised, acknowledged, resolved, or expired with nobody
  answering. Tick any combination; raised is the default.
- **Only for an emergency**, if it should stay quiet for everything else.

The shortcut gets the alert as its input. In the shortcut, use **Get Dictionary from
Input**, then read `alert` → `title`, `alert` → `urgency`, `state` (`fired` or
`cleared`), `event`, and `link`, the incident's lasting link. Use it to post to a team
chat, log the incident to Drafts or Obsidian with a link back to its record, turn on
lights through HomeKit, or start a Focus.

**Test fire** runs it once so you can check it. If the shortcut fails twice in a row, the
station raises an alert saying why, in the words Shortcuts gave. A shortcut is an
automation in addition to the station's alarm, never instead of it: the takeover, the
sound and any sirens behave exactly the same.

## Spotlight

Type part of an alert's title into Spotlight and AlertRoster's open alerts, and those
closed in the last day, are among the results. Choosing one opens its incident record.

Spotlight gets the title, the status, the source and the times, and nothing else: never
an alert's details, a position or anything about the person it concerns. An alert drops
out of Spotlight a day after it closes.

It's on for the operator station and off on the wall display and in kiosk mode, because
a shared screen isn't anyone's own Mac. Turn it off with **View → Show Alerts in
Spotlight**, which also removes everything AlertRoster put there.

## Focus filters

A Focus can change how the station looks and what it announces. In **System Settings →
Focus**, choose a Focus, then **Add Filter** under *Focus filters* and pick
**AlertRoster**:

- **Layout:** the wall display or the operator station while this Focus is on, for
  example the wall display in an *On shift* Focus.
- **Theme:** light or dark.
- **Announce Non-Emergency Alerts:** turn this off and a new alert that isn't an
  emergency comes in without its short sound. It still appears on the board.
- **Quiet Low-Urgency Alerts From:** pick sources, such as a home lab, whose low-urgency
  alerts should come in without a sound.

While a Focus has quieted anything, a line at the bottom of the station says so, in
both layouts, naming the quieted sources and saying that emergencies still sound. It
stays until the Focus ends, so someone who walks up later can tell that new alerts are
arriving without a sound.

When the Focus ends, the layout and theme go back to what they were. The filter changes
only this station's screen and sound: alerts are paged, and outputs and sirens run,
exactly as before.

**Emergencies are never filtered.** The emergency screen takeover, its alarm and its
minimum volume are the same in every Focus.

## `alertroster://` links

A link can bring up an alert, but it **can't change one**. Any web page, email or QR code
can put a link in front of a station, and opening a link isn't the same as deciding to
acknowledge an alarm. To act on an alert, use AppleScript or Shortcuts.

| Link | Does |
|---|---|
| `alertroster://incident/<id>` | Opens the incident's record. This is the link to keep, because it works for as long as AlertRoster keeps the incident, which is for good. |
| `alertroster://alert/<id>` | Brings the station forward with that alert selected. Once the alert has closed, it opens the alert's record instead. |
| `alertroster://thread/<id>` | Opens the incident's thread. |
| `alertroster://search/<id>` | Opens the search panel. |

An alert's id is its **id** property in AppleScript, or its ID in Shortcuts. A link that
can't be followed says why at the bottom of the board.

## Hookmark, and links to an incident

Every incident has a **record**: its whole history, open or closed, as AlertRoster keeps
it. Who was paged at each step, who answered and when, silences and reassignments, the
search, and how it ended. Open it with **Record (D)** on the selected incident, or
**Open record** in Alert history.

The record's link, `alertroster://incident/…`, keeps working after the incident closes
and after the station's 24-hour history has forgotten it. That makes it the thing to
link a search's case notes, map exports, emails and photos to. **Edit → Copy Link to
Incident** (⌥⌘C) puts a Markdown link to whatever incident is in front of you on the
clipboard, ready for Drafts, Obsidian, OmniFocus or a note.

An alert raised on this station that never reached AlertRoster has a record only while
the station holds it (24 hours after it closes), and its link says `alertroster://alert/…`
rather than `…/incident/…`.

### Hookmark

[Hookmark](https://hookproductivity.com) can link any two things on your Mac and take you
back to either. To teach it AlertRoster, open **Hookmark → Settings → Scripts**, add
AlertRoster, and paste these two scripts.

**Get Name:**

```applescript
tell application "AlertRoster" to get current title
```

**Get Address:**

```applescript
tell application "AlertRoster" to get current link
```

Then, with an incident selected on the board or its record window in front, invoke
Hookmark as usual. A hooked note opens straight back to the incident's record, even
months later.

The same two scripts work for the API Tester. With its window in front, they give the
selected request or folder, so you can hook a request to the ticket, spec or note it
belongs with. The link opens the API Tester with that request selected.

Links you can copy yourself:

| Where | Gives |
|---|---|
| **Edit → Copy Link** in the API Tester (⌥⌘C), or **Copy Link** on a request or folder in the sidebar | the request or folder |
| **⋯ → Copy Link to This Exchange** above a response | that response, from the request's history, for as long as history keeps it |

An API Tester link opens and selects things; it never sends a request. A link that would
send something or switch environment asks first.

## The responder report link

**share report** in AppleScript and **Share Responder Report** in Shortcuts hand back the
report's link. Anyone with that link can see the subject's photograph and last positions
without signing in. Send it only to the people searching. It stops working when the
report is withdrawn in the station's Report window.
