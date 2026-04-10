# already-in-flight

A Claude Code slash command for Firefox PMs. Runs a three-source workstream collision check before a feature idea gets invested in — catching in-flight or completed work across the Firefox codebase, Jira backlog, and Mozilla's discontinued-feature history.

Platform and Front-End teams operate on separate planning cycles. A collision that surfaces at the wrong moment costs weeks. This check runs in under a minute.

---

## What it checks

| Source | What it finds |
|---|---|
| **Searchfox** (`mozilla-central`) | Existing implementations, partial matches, or related components in the Firefox codebase |
| **Jira** (via Atlassian MCP) | In-progress, in-review, backlog, and recently closed tickets across Platform and Front-End teams |
| **Killed by Mozilla** | Discontinued Mozilla features that resemble the idea — graveyard context before pitching |

## Output

A structured collision report with a single verdict:

- **All Clear** — no known collision, proceed
- **Codebase Hit** — existing implementation found; flag to engineering before prototyping
- **Jira Hit** — someone is queued or actively working on this
- **Both Hit** — codebase + active Jira work; coordination call, not a blocker
- **Historical** — a prior Mozilla attempt was discontinued; review the context before re-pitching

---

## Installation

1. Copy `already-in-flight.md` into your Claude Code commands directory:

   ```
   cp already-in-flight.md ~/.claude/commands/already-in-flight.md
   ```

2. Ensure the **Atlassian MCP** is connected in your Claude Code session. The Jira check requires it — running without it produces an incomplete result.

   To verify: run `! claude mcp list` in the Claude Code prompt and confirm `atlassian` appears as connected.

---

## Usage

```
/already-in-flight <feature idea or capability>
```

Examples:

```
/already-in-flight passkeys
/already-in-flight picture-in-picture on Android
/already-in-flight WebGPU shader compilation cache
```

If the input is ambiguous, the skill will ask one clarifying question before proceeding.

---

## Requirements

- [Claude Code](https://claude.ai/code)
- Atlassian MCP connected (for Jira access) — see [mcp-remote](https://www.npmjs.com/package/mcp-remote)
- Access to [Searchfox](https://searchfox.org/mozilla-central/source) (public, no auth required)

---

## Notes

- The Jira check is the primary signal for in-flight team work. If the Atlassian MCP is unavailable, the skill will stop and surface reconnection instructions rather than run a partial check.
- Searchfox searches are run against `mozilla-central`. Results reflect the current state of the tree.
- The Killed by Mozilla check uses [killedbymozilla.com](https://killedbymozilla.com/). A graveyard entry is context, not a hard stop.
