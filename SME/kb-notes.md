# Knowledge Base notes (sanitized)

Recorded 2026-10-19 from the preserved ToolBook Knowledge Base that JohnDoe1 continues to host after SumTotal retired the product on 31 December 2021. Personal name, email, and host URL are omitted per repository rules.

## Activation (post-EOL)

Activation began with ToolBook 9.5. A license key is checked in real time against the SumTotal server. One license covers a primary machine plus a secondary machine for the same user. Deactivation (Help → License Activation → Deactivate) was required to move a license.

After EOL the activation servers are offline. No new activation is possible. There is no bypass. Existing activations on already-activated machines continue to work. The 30-day trial still functions on a fresh install but cannot be converted. Manual activation (Activation ID exchanged offline) is no longer usable.

## 11.5 Patch 4

Released 4 August 2015. Supersedes earlier patches. The base TB115 installer does not include it.

Fixes include:
- Web publishing errors ("Not an object: <target>", Popup Media Frame property errors)
- Internet Explorer 11 compatibility
- Universal Media Player Time Markers saving
- Bookmarking checkbox always grayed out
- Chapter Title field limit raised from 64 to 255 characters
- False "SCORE Actions Editor logic detected" in the DHTML publishing log
- About dialog and Startup screen show the installed patch level
- Microsoft Edge handling

Install requires Administrator rights. Verify via the Startup screen caption "ToolBook 11.5 Patch 4".

## File corruption

Symptoms: reproducible crash ("stopped responding") or the message "The last save was interrupted. This book may be corrupted."

Avoidance: never Save after a crash; prefer Save As to numbered archives; enable View | Options | Backup.

Recovery (in order of preference):
1. Repair Kit utility — copies objects, pages, backgrounds, and resources into a new book, removing many forms of corruption.
2. Copy/paste objects page-by-page into a blank book (two instances of ToolBook).
3. Insert → Pages from the damaged book, selecting ranges to skip suspect pages.

## Other notes relevant to the 8.1 work

- 11.5 is 32-bit and runs on 64-bit Windows. Versions before 9.0 are 16-bit and do not.
- MSXML 4 is required; MSXML 6 is not a substitute.
- Debug Mode for DHTML export exposes AICC/SCORM traffic (checkbox in the Publish to Web dialog).
- Content rectangles, polygons, and OpenScript remain as previously recorded. The Knowledge Base does not describe the 8.1 on-disk record layout.

These notes are historical support material. They do not change the 8.1 binary findings.
