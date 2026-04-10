# already-in-flight

A Claude Code slash command for Firefox PMs. Scans three sources to find out whether work on a feature idea is already underway — so you can join forces early instead of discovering the overlap late.

Platform and Front-End teams operate on separate planning cycles. When both sides are pulling toward the same thing, that's not a problem to avoid — it's leverage. This check surfaces that signal before discovery investment stacks up on either side.

---

## What it checks

| Source | What it finds |
|---|---|
| **Searchfox** (`mozilla-central`) | Existing implementations, partial matches, or related components in the Firefox codebase |
| **Jira** (via Atlassian MCP) | In-progress, in-review, backlog, and recently closed tickets across Platform and Front-End teams |
| **Killed by Mozilla** | Discontinued Mozilla features that resemble the idea — historical context before pitching |

## Output

A structured signal report with a single verdict:

- **All Clear** — no known overlap; proceed with full context
- **Codebase Match** — existing implementation found; loop in engineering early to understand what's extensible
- **Jira Match** — a team is already queued or actively working on this; good time to align on scope and share resources
- **Both** — codebase work exists and a team is in flight; coordinate before investing further — this is often the fastest path to shipping
- **Historical** — a prior Mozilla attempt was discontinued; understand what changed before re-pitching

When Jira surfaces active Front-End work, the default move isn't to stand down — it's to reach out. Platform can often accelerate what Front-End is building by contributing at the architecture layer, sharing ownership, or removing a blocker they didn't know existed. Finding overlap early is how teams ship faster together.

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
- The Killed by Mozilla check uses [killedbymozilla.com](https://killedbymozilla.com/). A historical entry is context, not a hard stop.
