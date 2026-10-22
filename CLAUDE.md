# Project: Automated Documentation Sync (Agentic SDLC Capstone)

## Context
This repo is built entirely through a Claude Code agentic SDLC pipeline. Each phase
is executed by a dedicated subagent in .claude/agents/.

## Global Rules for Every Subagent

### Rule 1 — No Fabrication
Never fabricate data, file contents, or test results. If unknown, mark explicitly as
"Not Found" and flag it, or ask the human.

### Rule 2 — Citation
Always cite which source doc you reasoned from (requirements.md, architecture.md, etc.).

### Rule 3 — No Secrets
Never output or hardcode secrets, tokens, or credentials in any artifact.

### Rule 4 — MANDATORY HUMAN APPROVAL GATE (applies to ALL agents)
1. Every agent produces its artifact as a DRAFT first (print it in chat / save with a
   `-draft` suffix if the agent has write access).
2. The agent must then explicitly ask: "Please review the draft above. Reply APPROVE
   to finalize, or tell me what to change."
3. The agent must NOT:
   - Finalize/overwrite the real filename (e.g. requirements.md) until the human
     replies with explicit approval (e.g. "approve", "looks good", "confirmed").
   - Recommend or hand off to the next phase's agent until approval is given.
4. If the human requests changes, the agent revises the draft and asks again — this
   loop repeats until approval.
5. Once approved, the agent states clearly: "✅ Approved and saved to docs/<file>.md.
   You may now proceed to the next phase: <next-agent-name>."
6. Reviewer agents (design-reviewer, code-reviewer) have an ADDITIONAL gate on top of
   this: even after their report itself is approved as a document, the SDLC cannot
   advance while any **High** priority finding in that report remains unresolved.
   The human must resolve/re-review those findings (looping back to the responsible
   agent, e.g. solution-architect or implementer) before the gate opens.

## Phase Order (strict, enforced by the approval gate above)
requirements-analyst → solution-architect → design-reviewer → implementation-planner
→ implementer → code-reviewer → verifier → pr-creator