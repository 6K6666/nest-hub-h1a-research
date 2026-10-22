# Contributing evidence

Contribute a result another person can check. Put the device, exact command, output, and limit beside the claim.

## Evidence format

For a device observation, record:

- Device class: retail or demo. Add model or board revision when known.
- Capture date, device build, host OS, and tool version.
- Exact read-only command or UI action & sanitized output.
- What the result proves, plus any condition that could change it, such as boot mode or update channel.

For source analysis, link the repository, exact commit or tag, file, and relevant function or lines. Hash local files when comparing copies. Name the build target; say when it isn't known.

Use these labels: **Observed**, **Source-backed**, **Reported**, **Hypothesis**, and **Unknown**. A source match doesn't establish a shipped-binary match. A demo report doesn't establish a retail reproduction.

## Device safety and privacy

Retail devices stay read-only in this project. Don't submit unlock, flash, erase, slot-change, recovery, boot-file staging, or undocumented OEM-command procedures as routine reproduction steps. Document the exact operation, expected effects, and rollback before anyone considers a device-changing test.

Remove serial numbers, MAC addresses, account identifiers, tokens, private URLs, and home-network details from logs. Don't upload proprietary firmware or private transcripts. Link public material & get permission before publishing someone else's files.

## Pull requests

Keep claims beside their sources. Separate observations from interpretation & date version-sensitive facts. Update the README status table when new evidence changes the result. A short correction with a source beats a broad rewrite.

