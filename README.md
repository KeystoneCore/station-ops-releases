# Station Ops — downloads

Installers for **Station Ops**, a maintenance and readiness record for a rescue
station. This repository holds downloads only: no source code lives here.

## Status: unsigned alpha

Builds published here are **alpha** builds for named testers. They are:

- **unsigned** — Windows SmartScreen may warn on first run. If it does, choose
  **More info** → **Run anyway**. Do not switch SmartScreen off;
- **not qualified for operational use** — nothing here should be relied on for
  a station's real records yet;
- **Windows 10 or newer, 64-bit only.**

## Installing

Take the newest release from the [Releases](../../releases) page and run the
`StationOps-Setup-*.exe`. It installs for your user only and needs no
administrator.

Check your download against the `SHA256SUMS.txt` published beside it before
running it.

The portable `.zip` beside each installer is for diagnosis. Install with the
Setup executable.

## Your station's records

The installer does not touch them. They live under
`%APPDATA%\com.keystonecore.station\Station Ops` and stay there through
install, upgrade and uninstall. Uninstalling is not a way to reset a station,
and it is not a backup — take your own copy from *Settings → Data on this
device → Export a copy*.

## Reporting a problem

Say which installer file you used — its filename carries the exact build — what
you did, and what happened. Please do not attach a station's real records.

---

Published by Keystone Core (Pty) Ltd. Not published by, or on behalf of, the
NSRI.
