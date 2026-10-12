# First run

This page takes a newly installed station to a board that shows your account's incidents
and alarms when one arrives. Allow about ten minutes.

Keyboard shortcuts are written with **Ctrl**. On a Mac, use **⌘ Command** instead.

## 1. Open AlertRoster and let it find the receiver service

For the first few seconds the station shows that it isn't connected yet. This is normal:

- A coloured banner across the top: *No alert data has been received. New alerts will
  NOT appear here until the service answers.*
- The board: *NOT CONNECTED — open alerts cannot be shown.*
- The bottom of the window: *● Looking for the receiver service…* or *● Starting the
  receiver service…*, then *● Connecting to the receiver service…*

Within a few seconds the banner goes away, the bottom of the window says **● Receiver
service connected**, and the board says **No open incidents.**

The banner exists for a reason. Whenever the station loses contact with the receiver
service, the banner comes back, and the bottom of the window shows the time of the last
data (*STALE — last data 14:32:05, 40s ago*). A board that has stopped updating always
says so.

If the banner stays up, or the bottom of the window says **NO RECEIVER SERVICE** or
**RECEIVER SERVICE UNAVAILABLE**, see [Troubleshooting](troubleshooting.md).

## 2. Sign in to your account

1. Choose **Service → Account…** (Ctrl+Shift+U), then **Sign in…**
2. Leave **Server** empty. It signs in to `https://alertroster.com`.
3. Enter the email address you use for AlertRoster and choose **Email me a code**.
4. Type the 6-digit code from the email into **Code**. The code expires after 10
   minutes. If your account has a password, you can type it into **Password** instead.
5. If your email address belongs to more than one account, an **Account** list appears.
   Choose one, request a new code, and sign in again.
6. Choose **Sign In**.

The **Account** window now shows **Signed in.** and who the station is signed in as.

The receiver service keeps the sign-in, not the window. Closing AlertRoster doesn't sign
you out, and the sign-in survives a restart. It is stored in your computer's secure store:
the keychain on macOS, the Credential Manager on Windows, and on Linux encrypted with a
key belonging to the machine, so the service can use it before anybody has logged in. The
station never writes a sign-in to disk unprotected. Where it can't store one securely,
the Account window says *This machine cannot store a sign-in securely.* and
**Sign in…** is greyed out.

**The station now shows your account's incidents.** An incident raised by your account,
such as a missed check-in, appears on the board with its position when there is one. It
takes over the screen when it is an emergency. The **Check-in** menu lists your check-ins,
and each armed one offers **I'm here** and **+15 minutes**.

The sign-in is for this computer's receiver service, not for a person. Anyone using this
station acts as the account that signed it in.

To sign out, choose **Service → Account…**, then **Sign out**.

## 3. Raise a test alert

Before you rely on the station, check that the alarm actually works:

1. Choose **Service → Raise a test alert** (Ctrl+Shift+T).
2. The screen is taken over and the alert sound plays. This is what a real emergency
   looks like on this station.
3. Choose **Acknowledge**, or press Space or Return. The Acknowledge button stays inactive
   for a moment when the alert first appears, so a key already being pressed can't
   dismiss it by accident. Escape never dismisses it.
4. Acknowledging stops the alarm. The alert stays on the board as acknowledged. Select it
   and press **R** (or choose **Resolve**) to close it.

### Showing the station without an alarm

To show somebody the map and the incident screens without sounding anything, choose
**Service → Show a demo incident** (Ctrl+Shift+D). It opens an already-acknowledged
incident with a hiker's track on the map, marked DEMO everywhere it appears. Nobody is
paged, nothing sounds and no siren or integration fires. Choose the menu item again to
go back to it, and resolve it to take it off the board.

### What the station sounds like

The sound tells you how urgent an incident is before you read anything:

- **An emergency** takes over the screen and plays its alarm until somebody
  acknowledges it: two tones alternating continuously, which is the alarm the station
  has always made. An emergency always sounds like this, whatever its urgency.
- **A new incident that is not an emergency** does not take over the screen. The
  station plays one short sound so you know it has arrived: the same two tones for a
  high-urgency incident, or a double blip followed by silence for a low-urgency one.

**View → Alert sound** has the settings:

- **Built-in tone** or **Sound file on this machine** chooses the emergency alarm. A
  sound file replaces the alarm only; the short sound for a new incident is always the
  built-in one.
- **Output** chooses which speaker the station plays on, for a machine wired into a PA
  or an amplifier. If that output is not connected when an alert sounds, the station
  plays on the system default instead, and the menu says so.
- **Minimum volume during an alarm** makes sure an emergency can be heard. While the
  alarm is sounding, the station unmutes the output and raises it to at least this
  level (50% to 100%, 70% unless you change it), then puts your volume back when the
  alarm stops. If you turn the volume up during the alarm, your level is kept. Muting
  the machine does not silence an emergency; acknowledging it does. The short sound
  for a new, non-emergency incident plays at whatever volume the machine is set to.

On Linux the minimum volume needs `pactl`, which is in the `pulseaudio-utils` package on
Ubuntu and Debian and in `libpulse` on Arch and Omarchy. Without it the alarm still
plays, at the machine's current volume.

## 4. Connect something that wakes a room

A screen only alerts somebody who is looking at it. If nothing is set up to make noise,
the bottom of the window says: *Nothing configured here can wake someone who is not at a
screen.* Choose **Dismiss** to hide the message, or set something up:

1. Choose **Service → Outputs…** (Ctrl+Shift+O), then **Add…**
2. Pick what to fire:
   - A relay board driving a siren, strobe, horn or bell (including a relay module on a
     Raspberry Pi's GPIO header).
   - An HTTP request on your network, for example to Home Assistant or ESPHome.
   - A command on this computer.
   - On a Mac, one of your shortcuts, with the alert as its input. See
     [Automate a Mac station](macos-automation.md).
   - An AWS SNS topic, SES email or IoT Core topic. These need **Service → AWS…** set up
     first.
3. Select the new output and choose **Test fire**, then **Test clear**. Test fire behaves
   exactly like a real alert. A relay stays on no longer than its safety limit.

Outputs fire when an alert is raised and clear when it is acknowledged, resolved or
expires.

If two alerts in a row cannot be delivered to an output, the station raises an alert
about that output, saying what the last error was. It clears itself as soon as anything
reaches the output again, and editing the output takes it down too. A single failure is
not reported: the station has already retried it, and one is usually a blip.

## 5. Connect integrations

Home Assistant, the Omarchy desktop integration, Asterisk and other tools on your network
send alerts to a station after they are paired with it:

1. Choose **Service → Pairing…** (Ctrl+Shift+P).
2. If the integration runs on a different computer, tick **Accept sources from the LAN
   (off: this machine only)**. The connection on your network is not encrypted, and the
   pairing is what authorizes each request, so leave this off unless something on another
   computer needs it.
3. Choose **Open pairing window**. An eight-digit code appears, for example `1234 5678`.
4. Enter the code in the integration within five minutes. Three wrong codes close the
   window.

The integration appears under **Paired**, together with what it is allowed to do. To
unpair it, select it and choose **Revoke**.

## 6. Escalate off-site when nobody here answers (optional)

On its own, a station keeps local alerts on your network. For example, an alert from
Home Assistant stays on the station and goes nowhere else. With an integration key, the
station also opens every local alert with AlertRoster, so your roster's phones are paged
if nobody at the station acknowledges it in time:

1. At [alertroster.com](https://alertroster.com), open **Sources**. Create a source for
   this station, or open an existing one. Choose **Mint sync key** and copy the key, which
   begins `ark_sync_`. It is shown only once.
2. On the station, choose **Service → Cloud…** (Ctrl+Shift+C).
3. Set **Host** to `https://alertroster.com`, paste the key into **Integration key**, and
   choose **Save**.

The key is stored in the same secure store as the sign-in. The **Status** section shows
whether the key is stored and when AlertRoster was last reached. With no key set, nothing is sent off this computer.
**Remove key** turns this off again. Minting a key is limited to account administrators.
The Cloud window only accepts a key on the computer the receiver service runs on.

## 7. Who is on call, and handing a shift over (optional)

Once the station is signed in to your account, **Service → On-call roster…**
(Ctrl+Shift+R) shows the rota your account keeps at alertroster.com, and lets you move a
shift without reaching for a phone.

At the top, choose a schedule and see who holds the pager right now, and why — on
rotation, or covering for somebody. If nobody is on call, the window says so plainly:
that is not a fault, but it does mean an incident on a source using that schedule pages
**every** responder in the account.

Three tabs:

- **Next 7 days** — every handover in the week ahead, with who takes over and when.
- **Rotation** — the layers behind it: who is in the rotation and in what order, how
  often it turns, and any hours a layer is restricted to.
- **Handovers** — every request you are a party to, asked of you or by you. Select one
  and choose **Accept**, **Decline**, or **Cancel request** for one you asked for. Only
  the person being asked can accept or decline; only the person who asked, or an account
  admin, can withdraw it.

Under **Move a shift**, choose who, whether you are handing your shift over or asking
somebody to cover a window of it, and the window itself, then choose **Ask**. Nothing on
the rota changes until they accept; when they do, the rota updates here at once, and
their phone is told. **Override…** puts somebody on call immediately with no request —
that is an account admin's to press, and the button says so when it is not yours.

Building the rota itself — creating schedules and ordering rotations — is done at
alertroster.com by an account admin.

## The menu bar

On a station somebody sits at, AlertRoster also shows in the menu bar (the system tray on
Windows and Linux). The icon carries a red dot while an alert is unacknowledged, and a
brighter one during an emergency. It turns grey when the station has lost its receiver
service. Its menu starts with what is open, or why the station isn't connected. It lists
the open alerts, each with **Show**, **Copy Link**, and **Acknowledge** or **Resolve**
where the alert offers them. Below that is your check-in.

Turn it off with **View → Show in Menu Bar**. Kiosk mode hides it, because a wall
display has nobody at the menu bar. On a Mac, **View → Hide Dock Icon** keeps AlertRoster
in the menu bar alone. AlertRoster's own menus (View, Service and the rest) go away with
the Dock icon, so the menu bar item then also offers **Show Dock Icon**, which brings
them back, and **Quit AlertRoster**. Its window is one click away in that menu either
way. The emergency screen takeover and alarm work the same whichever you choose.

## Keeping a station up to date

When a newer AlertRoster Desktop has been released, the station shows a line at the
bottom of the board naming the new version and pointing at the downloads page. It is a
note with a **Dismiss** button, not a pop-up, and it will not appear over an alert.

**The station never updates itself.** It does not download, install, or restart anything
— installing means running the installer for your platform again, which takes the board
off the wall for a minute, so it is yours to do when things are quiet. Your settings,
pairings and sign-in are kept across an upgrade.

A station that is signed in also appears in your account's device list with the version
it is running, so somebody with account access can see a machine left on an old build
without walking up to it. A station that is not signed in sends nothing.

## Keyboard reference

| Key | Does |
|---|---|
| **A** | Acknowledge the selected incident |
| **R** | Resolve the selected incident |
| **S** | Search panel for the selected incident |
| **P** | Responder report for the selected incident |
| **G** | Reassign the selected incident to another responder (an incident from your account) |
| **L** | Silence paging on the selected incident: quiets phones and this screen for a short while; relays and sirens stay on |
| **Y** / **N** | Approve or reject a coding agent's request on the selected incident. Opens a confirmation with room for a note (Ctrl+Return sends); only on an incident that is asking |
| **T** | The selected incident's thread: read and write the conversation everybody on it sees (Ctrl+Return sends) |
| **D** | The selected incident's record: its whole history, open or closed, and a link to it that keeps working after it closes |
| Ctrl+Alt+C (⌥⌘C on a Mac) | Copy a link to the incident in front of you: the record window if it is open, the selected incident otherwise |
| Ctrl+1 / Ctrl+2 | Operator station / wall display layout |
| Ctrl+Shift+U | Account |
| Ctrl+Shift+P | Pairing |
| Ctrl+Shift+O | Outputs |
| Ctrl+Shift+C | Cloud |
| Ctrl+Shift+H | Alert history: every open alert, and every one closed in the last 24 hours. **Open record** on a row shows its record |
| Ctrl+Shift+R | On-call roster: who is on call, the week ahead, and handovers |
| Ctrl+Shift+T | Raise a test alert |
| Ctrl+Shift+D | Show the demo incident: the map and incident screens, with no alarm |
| Ctrl+Shift+K | Kiosk mode |

Next: [Wall displays](wall-display.md).
