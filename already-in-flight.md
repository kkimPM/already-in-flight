You are running an **"Is Someone Already Working on This?"** workstream alignment check for Karen Kim, Senior PM (Media & Graphics + Privacy) at Mozilla Firefox.

The purpose of this check is to surface in-flight or completed work in the Firefox codebase and Jira backlog **before** a feature idea gets invested in — either during product discovery, prototyping, or a competitive analysis. Platform and Front-End teams historically work in separate planning cycles; finding overlap early is an opportunity to combine forces and ship faster, not a reason to stand down.

---

## Pre-flight: Atlassian MCP Check (required before proceeding)

**Run this before anything else:**

Call `mcp__atlassian__atlassianUserInfo` directly. A live, authenticated connection returns Karen's name, email (`kkim@mozilla.com`), and account status. This confirms the server is not just registered but actually responding — `claude mcp list` only checks registration, not auth.

- **If the call returns account info:** proceed to Input.
- **If the call fails or returns an error:** stop and surface this to Karen immediately:

> **Jira is unavailable — the Atlassian MCP is not connected in this session. The workstream alignment check cannot complete without it. Jira is the primary signal for in-flight team work; skipping it defeats the purpose of the check.**
>
> To reactivate:
> 1. **Try restarting Claude Code first.** If the OAuth token is still valid, it reconnects silently.
> 2. **If that fails:** run `rm ~/.mcp-auth/mcp-remote-0.1.37/*` in your terminal, then restart Claude Code. A browser Atlassian OAuth window will open — complete the login.
> 3. **Verify:** run `! claude mcp list` in the Claude Code prompt and confirm `atlassian` appears before re-running `/already-in-flight`.

Do not proceed with a partial check and present it as complete. If Karen explicitly instructs you to run a Bugzilla-only check as a stopgap, note clearly in the signal report that the Jira check was skipped and the verdict is incomplete.

---

## Input

The user has provided a feature idea, concept, or capability — either as a freeform description or as output from a competitive analysis or /bet run. If the input is ambiguous, ask one clarifying question: **"What is the user-facing behavior you're checking for?"** Then proceed.

---

## Step 1 — Searchfox: Codebase Scan

Search `mozilla-central` for any existing implementation that resembles the feature.

**Using `searchfox-cli` (preferred — faster, no browser):**
- Symbol search: `searchfox-cli --symbol <ComponentName>` for any UI component names you can infer from the feature description
- Full-text search: `searchfox-cli --text "<keyword>"` — try 2–3 keyword variants (e.g., the feature name, the UX pattern name, likely API names)
- File-tree browse if the feature maps to a known area: `browser/components/` (desktop UI), `toolkit/` (shared primitives), `browser/extensions/newtab/` (newtab features)

**Using [Searchfox web](https://searchfox.org/mozilla-central/source) (fallback):**
- Full-text search across the tree with regex support
- Search by component type if behavioral: "panel", "doorhanger", "toolbar button", "notification bar", etc.

**What to flag:**
- An existing implementation that covers the same user-facing behavior → **Match: Implemented**
- A component or module that partially covers it → **Match: Partial — may be extensible**
- No code found → **Clear in codebase**

For any hit, capture: file path, component/function name, a Searchfox link, and a one-sentence description of what it does.

---

## Step 2 — Jira: In-Flight Work Scan

Query Jira via the Atlassian MCP for tickets indicating someone is actively working on — or has recently worked on — this feature.

**Search strategy:**
- Run at least 3 keyword searches: the user-facing feature name, the likely engineering component or API term, and any spec or standard name if applicable
- Filter for statuses: **In Progress**, **In Review**, **Backlog**, **To Do**
- Also check recently **Done** / **Closed** tickets from the last 6 months — a recently closed ticket may mean it already shipped or was deliberately descoped (both are important signals)
- Search across both Platform and Front-End team projects if project keys are known; if not, search broadly and note the team/org from any hits

**What to flag:**
- An in-progress or in-review ticket → **Active match: staffed and in flight**
- A backlog ticket with an owner or recent activity → **Queued match: may be coming**
- A recently closed ticket → **May have shipped or been descoped** — check resolution
- No tickets found → **Clear in Jira**

For any hit, capture: ticket ID (as a link), title, status, assignee, last updated date, and which team/project it belongs to.

---

## Step 3 — Killed by Mozilla

Check [Killed by Mozilla](https://killedbymozilla.com/) for any discontinued Mozilla product or feature that resembles the idea. A graveyard entry isn't a hard stop — but it's context that sharpens the pitch or saves the trip.

Flag: feature name, approximate discontinuation date, and any inferred reason if visible on the page.

---

## Step 4 — Signal Report

Synthesize findings into a brief, structured report. Use this format:

---

**Already In Flight? — [Feature Name]**
*Run: [date]*

**Verdict:** [one of: All Clear / Codebase Match / Jira Match / Both / Historical (Killed by Mozilla)]

| Check | Finding | Detail |
|---|---|---|
| Searchfox (codebase) | [Clear / Partial / Match] | [file path or "nothing found"] |
| Jira (in-flight) | [Clear / Queued / Active] | [ticket ID + title or "nothing found"] |
| Killed by Mozilla | [Clear / Historical match] | [feature name + date or "nothing found"] |

**Recommended action:**
- **All Clear:** proceed — no known overlap. Note this check was run and when.
- **Codebase match:** flag the file/component to engineering before prototyping. Ask: is this extensible, or would the feature require a fork?
- **Jira match (queued):** surface the ticket to the relevant EM before investing further. Align on ownership before the idea advances.
- **Jira match (active — Front-End team):** don't drop the idea. Reach out to the relevant EM and explore whether Platform can accelerate or deepen the work — architectural support, platform-layer contributions, or co-ownership. When Front-End is working near the top of the stack, Platform can often unlock things from below. Combining forces is how teams ship faster together.
- **Jira match (active — Platform team):** align directly. Determine whether to merge workstreams or contribute — duplication within Platform is the higher-friction case.
- **Historical match:** review the discontinuation context before proceeding. Ask: has the constraint that killed it changed?

---

## Output

Print the signal report to the conversation. Do not save it to the vault automatically — this is a quick check, not a persistent artifact. If the user wants to save it, they will ask.

If a Jira match is found, offer to pull the full ticket thread for context.

---

## Maintenance

The canonical source for this skill is the GitHub repo at https://github.com/kkimPM/already-in-flight. The local copy lives at `~/.claude/commands/already-in-flight.md`.

**Any time this file is edited locally, push the change to the repo immediately** using the GitHub Contents API via `gh api --method PUT`. Both copies must stay in sync — the repo is the source of truth for reinstalls and sharing.

To verify they match: compare the local file against `gh api "repos/kkimPM/already-in-flight/contents/already-in-flight.md" --jq '.content' | base64 -d`.
