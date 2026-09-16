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
the keychain on macOS, the keyring on Linux. The station never writes a sign-in to disk
unprotected. Where it can't store one securely, the Account window says *This machine
cannot store a sign-in securely.* and **Sign in…** is greyed out.

> **Windows:** this release can't store a sign-in securely on Windows, so a Windows
> station can't be signed in yet. It still raises, shows and alarms on local alerts, and
> drives outputs and integrations (steps 3 to 5). Showing your account's incidents and
> escalating off-site (step 6) currently need a Mac or a Linux station.

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

To choose the sound, use **View → Alert sound**.

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
   - An AWS SNS topic, SES email or IoT Core topic. These need **Service → AWS…** set up
     first.
3. Select the new output and choose **Test fire**, then **Test clear**. Test fire behaves
   exactly like a real alert. A relay stays on no longer than its safety limit.

Outputs fire when an alert is raised and clear when it is acknowledged, resolved or
expires.

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

The key is stored in the same secure store as the sign-in, so this isn't available on
Windows yet either. The **Status** section shows whether the key is stored and when
AlertRoster was last reached. With no key set, nothing is sent off this computer.
**Remove key** turns this off again. Minting a key is limited to account administrators.
The Cloud window only accepts a key on the computer the receiver service runs on.

## Keyboard reference

| Key | Does |
|---|---|
| **A** | Acknowledge the selected incident |
| **R** | Resolve the selected incident |
| **S** | Search panel for the selected incident |
| **P** | Responder report for the selected incident |
| **G** | Reassign the selected incident to another responder (an incident from your account) |
| **L** | Silence paging on the selected incident: quiets phones and this screen for a short while; relays and sirens stay on |
| Ctrl+1 / Ctrl+2 | Operator station / wall display layout |
| Ctrl+Shift+U | Account |
| Ctrl+Shift+P | Pairing |
| Ctrl+Shift+O | Outputs |
| Ctrl+Shift+C | Cloud |
| Ctrl+Shift+H | Alert history: every open alert, and every one closed in the last 24 hours |
| Ctrl+Shift+T | Raise a test alert |
| Ctrl+Shift+K | Kiosk mode (macOS and Linux) |

Next: [Wall displays](wall-display.md).
