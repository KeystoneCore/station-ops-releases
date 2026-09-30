# Station Ops — downloads

Installers for **Station Ops**, a maintenance and readiness record for a rescue
station. This repository holds downloads only: no source code lives here.

## Status: closed-test alpha

Builds published here are **alpha** builds for named testers. They are:

- **not store-distributed** — operating systems can show a first-run warning;
  do not disable their security features;
- **not qualified for operational use** — nothing here should be relied on for
  a station's real records yet;
- packaged separately for Android arm64, Windows x64, Linux x64 and universal
  macOS (Apple Silicon and Intel).

## Installing

Take the newest release from the [Releases](../../releases) page. Each release
has a **Downloads** section that names the right file and its current platform
limitations. Android phones and tablets use the same adaptive arm64 APK; there
is no separate tablet build.

Check your download against the `SHA256SUMS.txt` published beside it before
running it.

Windows should normally use the Setup executable; its portable zip is for
diagnosis. macOS uses the universal zip, Linux the x64 tarball, and Android the
arm64 APK.

## Your station's records

An installer or package does not intentionally replace station records.
Uninstalling is not a way to reset a station, and it is not a backup — take
your own copy from *Settings → Data and backups → Export a copy* first.

## Reporting a problem

Say which installer file you used — its filename carries the exact build — what
you did, and what happened. Please do not attach a station's real records.

---

Published by Keystone Core (Pty) Ltd. Not published by, or on behalf of, the
NSRI.
