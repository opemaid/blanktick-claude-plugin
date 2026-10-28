# Blanktick for Claude

Ask Claude how secure this Windows PC is, and get answers read off the machine
instead of guesses. This plugin connects Claude Code to
[Blanktick](https://blanktick.org), a free, offline, open-source security-posture
scanner, through Blanktick's own read-only MCP server.

**Windows only.** Install Blanktick first from [blanktick.org](https://blanktick.org)
or build it from [github.com/opemaid/blanktick](https://github.com/opemaid/blanktick).
This plugin does not include or download Blanktick.

## What you get

- **The `blanktick` MCP server**: eight read-only tools.
- **The `security-checkup` skill**: tells Claude to report what could not be checked
  separately from what passed, and to give the score only alongside coverage.
- **`/blanktick:checkup`**: runs a full check-up and explains it.

| Tool | What it answers |
|---|---|
| `blanktick_scan` | Full scan: score, every finding with its fix, and the checks that could not run (~100 s) |
| `blanktick_facts` | The raw facts read off the machine, including the ones it would not answer |
| `blanktick_probe_script` | The exact PowerShell Blanktick runs, so you can read or re-run it |
| `blanktick_coverage` | What can and cannot be checked on this machine, and why |
| `blanktick_history` | Past scans from the local store |
| `blanktick_search` | Findings across stored scans, by severity, rule, category or date |
| `blanktick_changes` | What is new and what is gone since the previous scan |
| `blanktick_netlog` | Review a Wireshark capture you exported as CSV |

## What it runs, reads and sends

Everything here happens on your computer. Nothing is sent anywhere.

- **What it runs.** `bin/blanktick-mcp.cmd` finds `Blanktick.exe` and starts it with
  `--mcp`. It looks first at the `BLANKTICK_EXE` environment variable, if you set it,
  then in `%LOCALAPPDATA%\Blanktick`, `%LOCALAPPDATA%\Programs\Blanktick` and
  `%ProgramFiles%\Blanktick`. It prints nothing itself.
- **How Claude talks to it.** Over standard input and output (stdio). Blanktick opens
  no network socket, and its build fails if networking code is ever added.
- **What Blanktick reads.** To scan, Blanktick runs read-only PowerShell queries
  against Windows settings: firewall, Defender, BitLocker, updates, accounts,
  startup items, AI-assistant configs and similar. `blanktick_probe_script` shows
  the exact script.
- **What it writes.** Nothing unless you set `keepHistory=true` in
  `%APPDATA%\Blanktick\settings.conf`. With that setting on, each scan adds one
  summary line (rule ids, severities and score, no machine specifics) to
  `%APPDATA%\Blanktick\history.jsonl`.
- **What it never does.** No tool fixes a finding, changes a setting, deletes a file
  or quarantines anything. Claude recommends; you decide and act.

## Setup

1. Install Blanktick.
2. Install this plugin in Claude Code.
3. If you use the portable zip or a source build, set `BLANKTICK_EXE` to the full path
   of `Blanktick.exe`.
4. Ask, for example: "How secure is this PC?" or "What changed since my last scan?"

## License

AGPL-3.0, the same license as Blanktick.
