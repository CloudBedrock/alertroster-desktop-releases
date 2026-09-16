# Raspberry Pi

`AlertRoster-RaspberryPi-arm64.tar.gz` contains two programs, `AlertRoster` and
`alertroster-receiverd`, built for 64-bit Raspberry Pi boards. They are built against the
Qt Boot2Qt system image for Raspberry Pi and run on that image. They are **not** a package
for Raspberry Pi OS: there is no installer, no application menu entry and no service set
up to start at login.

A step-by-step guide to setting up a Raspberry Pi as a wall display isn't published yet.

## Putting the board on a screen today

Any TV or monitor with a web browser can show a station's board, without installing
anything on the screen itself. Set up a station on [Windows](windows.md), [macOS](macos.md)
or [Linux](linux.md), then pair the screen to it. [Wall displays](wall-display.md)
explains how.

## Who the tarball is for

It is for people already running the Boot2Qt image on their boards, who start programs on
it their own way. On that image, `alertroster-receiverd` must be running before
`AlertRoster` opens. Nothing starts either program automatically, so after a restart
both must be started again.
