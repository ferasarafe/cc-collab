---
name: "cc-collab"
description: "Runs a Claude + ChatGPT collaboration on ANY task (plans, code, docs, research, decisions, specs) through shared files in a user folder until both converge on one deliverable. Use when the user asks Claude and ChatGPT to work together, debate, or converge."
---

# CC Collab — Claude × ChatGPT (v12.0)
Two equal collaborators produce one usable deliverable. One holds the pen; neither has a correctness tie-break. User instructions override these defaults.

## 1. Setup
Claude creates TASK.md (≤1 page): deliverable/format, audience, done-when, must/avoid, writers and paths, required capabilities/review level, permissions, locked rulings, and compact §3–§6 rules. Batch unresolved blocking owner questions once; reuse explicit kickoff answers. Decide routine reversible choices within authorization; record a default and triggering event for optional OWNER questions. Silence never grants permission.
Each verifies source reads, own-file writes, required tool operations and monitoring. Failure: SETUP BLOCKED with the exact restriction; tell owner once, stop. Claude posts T1, supplies §8, and waits.

## 2. Files
TASK.md: Claude. CLAUDE.md/CHATGPT.md: each writes only its own; overwrite line 1, append turns. DRAFT.md and requested final format beside it: designated writer. DRAFT includes unresolved OWNER DECIDES, sources and `vN changed: §x` footer.
work/: coder's `<item>-vN.patch` and `<item>-vN-tests.txt`; reviewer's `<who>-<item>-vN-review.txt`. Never overwrite immutable vN handoffs. Keep supporting logs/checkouts/evidence in work/scratch/, linked but excluded from polling. No extra protocol files. Cite sources by file/line or page.
Receipts: baseline SHA, artifact sha12, commands/results, fresh/reused/unverified checks, evidence links.
Status: `STATUS · Tn · phase · candidate/hash · open: IDs · seen: peer Tn · evidence: path/turn · next: action`.

## 3. Mindset
Solve the brief; preserve owner commitments and locks. Unknowns: TBC plus OWNER DECIDES. Don't reopen authorized preparation. Omit unsolicited routine disclaimers; record verification limits once and update only when changed.
For code, independently investigate current source/history and task-required evidence; agree diagnosis and the smallest fix before coding. Before reading test assertions, reviewer derives expected behavior from requirements/platform behavior and identifies a plausible wrong fix the test must reject. RED/GREEN alone cannot establish correctness. Documents need requirement coverage, not artificial RED tests.

## 4. Flow
Bounded edits: Draft → Review → Accept. New work starts with ≤3 independent ideas each, posted before reading the peer's; writer merges v0.1. Code: one item at a time.
1. Candidate: code publishes one READY patch/receipt from a fresh clean-baseline apply, with RED on old code and GREEN on new; documents publish DRAFT vN with coverage checks. Stop revising until review, unless `SUPERSEDE vN: reason`; reviewer drops superseded work. Rerun checks affected by revisions.
2. Review: full artifact initially, then changed sections/evidence. Code: own worktree, reproduce RED/GREEN, inspect callers, run TASK-required external review. Post findings together, ≤6 R# IDs, highest impact first: `R3 §2: old → replacement · why`. R# means a defect or unmet requirement; style and wording go out as `IDEA`, nonblocking. No changes is valid.
3. Apply: ✓/✗ with reason for every ID; bump version/footer. Default: after two exchanges unresolved, record both positions in OWNER DECIDES/TBC; max three rounds. Limits never manufacture acceptance; follow TASK/user overrides.
4. Accept: read full saved artifact, compute hash, post `ACCEPT <file/version> · <sha12>`. Both must match. Changes invalidate only affected artifacts and dependent claims.
5. If TASK requires and authorizes branch/PR review, push branch, open draft PR, run that review and resolve findings through 1–4. Otherwise disclose that verification limit. Crash-language showed PR review catching a defect local CLI missed; tool silence is not correctness.
Preparation continues within authorization; sending/publishing/merging requires permission. Record owner changes immediately; designated writer updates TASK/DRAFT. Reopen affected items only.

## 5. Waiting and recovery
T1: `MONITOR: in-turn polling` or `scheduled · confirmed interval`, verified. File changes cannot wake inactive agents.
Waits ≤60s; first peer check after two minutes, then each minute. Poll status and top-level handoff names; read unseen turns only when changed. Stay active; a peer ending without a confirmed ≤1-minute wake-up means SETUP BLOCKED. After 20 minutes unchanged, tell owner once and stop.
After compaction/restart: TASK → both statuses → current candidate/receipt → unseen turns. Retrieve older history only for unresolved claims.

## 6. Turns
≤200 words excluding replacement text. `### Tn · who · HH:MM city`, real clock. Deltas only: findings, decisions, hashes, next action. No ACK-only turns, recaps, repeated boundaries or consecutive turns without peer response, except applying its review.

## 7. Saves
Edit in place with shell. Otherwise stage/edit/commit to the same path using expectedMtimeMs; re-stage and verify version/hash. On conflict, merge current bytes and retry; never force.

## 8. Startup
`cc-collab in <FOLDER>. Read TASK.md fully. You are an equal collaborator; <writing/research scope>. Verify required capabilities. Write only CHATGPT.md and authorized work/ files. T1: MONITOR (verified). First peer check after two minutes; thereafter every minute while active.`

## 9. Finish
Both accept every artifact and required checks are resolved: stop monitors, mark DONE with hashes and any pending owner action. Tell owner in ≤3 lines what is ready, unverified and awaiting a decision. Unresolved required decisions mean blocked, not converged.
