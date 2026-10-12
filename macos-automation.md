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

## The responder report link

**share report** in AppleScript and **Share Responder Report** in Shortcuts hand back the
report's link. Anyone with that link can see the subject's photograph and last positions
without signing in. Send it only to the people searching. It stops working when the
report is withdrawn in the station's Report window.
