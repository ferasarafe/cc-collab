# cc-collab — Claude Code × ChatGPT/Codex, one deliverable

A protocol skill. Two AI agents work in **one shared folder** on your disk, talk only through their own markdown files, and finish when both post `ACCEPT <file> · <sha12>` on the same bytes. You answer only lines marked `OWNER`. No copy-pasting between the two.

The skill text is [`skills/cc-collab/SKILL.md`](skills/cc-collab/SKILL.md) (v12.0). It was rewritten after a real run — a mobile crash fix reviewed by both agents — and every rule in it traces to a cost or defect observed there.

## What it's good for

Any task with a single deliverable the two agents can check: a code fix with tests, a plan, a spec, a review, a decision memo. One agent **codes/drafts**, the other **reviews**: applies the patch in its own worktree, reproduces RED/GREEN itself, runs the external reviewer you name (e.g. CodeRabbit), checks every caller, and posts defects only.

## Install

**Claude Code** — as a personal skill:

```bash
mkdir -p ~/.claude/skills/cc-collab && curl -fsSL https://raw.githubusercontent.com/ferasarafe/cc-collab/main/skills/cc-collab/SKILL.md -o ~/.claude/skills/cc-collab/SKILL.md
```

or as a plugin, inside Claude Code:

```text
/plugin marketplace add ferasarafe/cc-collab
/plugin install cc-collab@ferasarafe
```

**ChatGPT / Codex:** nothing to install. The second agent reads the rules from `TASK.md` (Claude writes them there, §1), so it works with ChatGPT given folder access, or with Codex CLI started in the same folder. If you use Codex skills, drop the same `SKILL.md` into `~/.codex/skills/cc-collab/`.

## Folder layout (one folder per task)

```
my-task/
├─ TASK.md        Claude writes: deliverable, done-when, roles, capabilities, OWNER questions, rules
├─ CLAUDE.md      Claude's turns      (line 1 = status line, then append-only ### Tn turns)
├─ CHATGPT.md     ChatGPT/Codex turns (same shape)
├─ DRAFT.md       working content (documents) — the pen-holder writes it
├─ <final file>   the requested format, once
└─ work/          <item>-vN.patch · <item>-vN-tests.txt · <who>-<item>-vN-review.txt
   └─ scratch/    logs, checkouts, raw evidence — kept, linkable, never polled
```

Each agent writes **only its own file(s)**. Patch names are immutable (`v1`, `v2`, …). Nothing else goes in the folder.

## Launch

1. Make the folder. Put source material next to it (or anywhere; TASK cites it by path).
2. **Claude Code** — open a session with the folder as working directory and paste:

   ```text
   Invoke the cc-collab skill. cc-collab in this folder. <one line: the deliverable and its final format>.
   You are <Lead|reviewer>; ChatGPT <codes|drafts> in CHATGPT.md and work/. Turn 1: verify <Sentry, CodeRabbit, git, …> and write access (else SETUP BLOCKED), record the baseline SHA, ask me the OWNER questions in one batch, and start your independent re-investigation. Write only CLAUDE.md, TASK.md, DRAFT.md and work/claude-* receipts. Stay in-turn and poll CHATGPT.md every 60 s; no turn limit; continue until both accept the same hashes.
   ```

   Claude writes `TASK.md`, posts `CLAUDE.md` T1 and prints the **startup line** for the other agent.

3. **ChatGPT (with folder access) or Codex** — new conversation in the same folder, paste the startup line Claude printed. It looks like:

   ```text
   cc-collab in <FOLDER>. Read TASK.md fully. You are an equal collaborator; <writing/research scope>. Verify required capabilities. Write only CHATGPT.md and authorized work/ files. T1: MONITOR (verified). First peer check after two minutes; thereafter every minute while active.
   ```

4. Answer any `OWNER Qn` lines in whichever chat you're in. Each carries a default and a trigger, so the run doesn't stall on you — but silence never grants a permission (push, publish, send, spend).
5. Watch line 1 of either file: `STATUS · T7 · phase · candidate/hash · open: R3,R5 · seen: peer T6 · evidence: … · next: …`. `DONE · <hashes>` means both accepted.

## How a code item flows

```
coder   : work/<item>-v1.patch + work/<item>-v1-tests.txt  (baseline SHA, RED on old, GREEN on new) → STOP
reviewer: own worktree from baseline → reproduce RED/GREEN → external review → callers → R1..R6 or ACCEPT <file> · <sha12>
coder   : ✓/✗ per R# → v2  (or SUPERSEDE v1: reason)
both    : ACCEPT on the same sha12 → next item
end     : if TASK authorises it, push branch → draft PR → PR-side review resolved → DONE
```

Rules that matter most, learned the hard way:

- **One READY candidate, then stop.** Posting v2/v3 before the first review triples the reviewer's work.
- **RED/GREEN proves the code changed, not that it's right.** The reviewer writes the expected value and a plausible wrong fix *before* reading the test. A test that passes the wrong fix is a defect.
- **R# = defects only.** Wording goes out as `IDEA`, non-blocking.
- **Say limitations once** (receipt or TASK), never per turn.
- **Equal authority.** The pen-holder has no tie-break; deadlock → both positions in `OWNER DECIDES`, you decide.

## Requirements

- Both agents must read and write the same folder without you relaying (Claude Code working dir; ChatGPT with folder/file access, or Codex CLI). If either can't, the run stops at `SETUP BLOCKED`.
- Both stay alive by in-turn polling (≤ 60 s). An agent whose turn ends without a confirmed wake-up has left the run.
- Name the external tools in TASK (git/GitHub, CodeRabbit, Sentry, …); both verify them in turn 1.

## Files here

- `skills/cc-collab/SKILL.md` — the skill (frontmatter + protocol, ~780 words).
- `.claude-plugin/` — manifest so Claude Code can install it as a plugin.

License: MIT.
