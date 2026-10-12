# cc-collab

### Tired of copy-pasting between Claude and ChatGPT?

“Here's what Claude said.” Copy. Paste. “Here's what ChatGPT thinks.” Copy. Paste. Somehow, **you became the messenger between two of the world's best AI agents.**

**cc-collab lets them talk through shared Markdown files instead.** They read each other's work, challenge ideas, make improvements, and keep going until both approve the same result. No more carrying messages between chats.

Set them up, give them a task, and go back to TikTok doomscrolling while Claude and ChatGPT do the work. They'll flag decisions that need you—you can check their progress whenever you like. Keep both sessions active so the conversation can continue.

**A proposal, a business plan, research, or a code fix:** one writes, the other checks, and both have an equal say.

cc-collab is a free **skill**—a set of instructions for the AI tools you already use. Bring your own Claude and ChatGPT/Codex sessions, give both access to the same folder, and let them collaborate.

[Get started](#get-started) · [See examples](#what-can-you-use-it-for) · [Read the rules](skills/cc-collab/SKILL.md)

## Why use two assistants?

Because “looks good to me” is more convincing after someone has actually checked the work.

- **Catch more than typos.** The reviewer can question an assumption, spot a missing requirement, or ask whether the evidence supports the conclusion.
- **Retire from messenger duty.** They exchange drafts and feedback in the shared folder. You keep your clipboard.
- **Know what is actually finished.** Both must approve the same saved version, not two slightly different drafts called “final-final.”
- **See what still needs you.** Decisions, missing information, and unfinished checks stay visible.

Two assistants can still miss the same thing. This gives your work another layer of review, not a guarantee. It also uses two AI sessions; savings in time or cost have not been measured.

## What can you use it for?

Bring them something worth a second look:

| Your task | Their job |
|---|---|
| “Write a proposal from these meeting notes.” | Draft it, check the price and promises, and catch anything the brief never agreed to. |
| “Help me choose between these business ideas.” | Build the case, challenge the assumptions, and explain the trade-offs. |
| “Turn this research into a useful summary.” | Organize the findings and check whether the sources support them. |
| “Write a clear product specification.” | Describe what should happen and look for gaps before someone builds it. |
| “Find and fix this bug.” | Investigate the cause, make a focused change, and independently check it. |

For a two-line email, one assistant is probably plenty. For work you will send, build, or make a decision from, a second pair of AI eyes can help.

## What happens after you give them a task?

**You brief them → they draft → they challenge → they improve → both approve.**

One holds the pen. The other asks the awkward questions. Neither gets to win an argument just because it wrote the first draft.

They check the result against your brief and tell you what is ready, what they checked, and what remains uncertain. If they need your judgment, they flag a question for you. An unresolved required decision means “blocked,” not “close enough, ship it.”

Sending, publishing, or merging the work still needs your permission.

## Before you wander off to TikTok

You need:

- **Claude Code and a second session in Codex**, or another ChatGPT environment that can read and write local files.
- **A folder both can access.** Think of it as their shared desk.
- **Whatever the job requires.** Source documents for a proposal; repository and testing access for a code fix.

**Two ordinary chat tabs and an uploaded file will not do it.** Both assistants need continuing access to that shared folder. Keep the sessions active, or use a verified way to wake them for updates. cc-collab provides the instructions; it does not open or connect the sessions for you.

If something is missing, they should explain it before starting. Better a clear setup problem than two agents confidently talking to themselves.

## Get started

### 1. Give Claude the skill

Run these two commands inside Claude Code:

```text
/plugin marketplace add ferasarafe/cc-collab
/plugin install cc-collab@ferasarafe
```

Prefer a manual install? The [developer details](#for-developers-and-the-curious) below have that covered.

### 2. Give them something to work on

Create a folder for the task and open Claude Code with access to it. Paste this and fill in the brackets:

```text
Use the cc-collab skill in [full folder path].

I want [the finished result and file format].
It is for [audience].
Use these sources: [files or links].
It must include [requirements] and avoid [constraints].

You and ChatGPT are equal collaborators. Set up the shared task,
check the access you need, and give me the startup message for
ChatGPT or Codex. Ask any essential questions in one batch.
```

A good brief can be simple: “Write a one-page Word proposal for this restaurant owner using these meeting notes. Keep the agreed price. Don't invent extra services.”

### 3. Introduce the other assistant

Open Codex, or a compatible ChatGPT environment, with access to the **same folder**. Paste the startup message Claude gives you.

Yes, one last copy-paste. After that, they exchange their reviews through the files.

Codex does not need a separate skill installation to join: Claude saves the task rules in the folder.

### 4. Let them work; check in when needed

Their latest status tells you where things stand. Three labels are useful:

- **`OWNER DECIDES`** — “We need your judgment on this.”
- **`SETUP BLOCKED`** — “We're missing access or a working way to check for updates.”
- **`DONE`** — “We both approved the required outputs, and the required checks are resolved.”

You do not need to supervise every exchange. You do need to answer required questions and review any remaining limitations before using the result.

## For developers and the curious

The friendly version ends here. These are the nuts and bolts behind it.

### How code changes are checked

The agents independently investigate, then agree on the cause and smallest fix before coding. The reviewer works out the expected behavior **before** relying on the writer's test assertions. A passing test with the wrong expectation is still a problem.

1. The writer publishes one `READY` patch and test record: baseline commit, file hash, commands/results, evidence links, and checks marked fresh, reused, or unverified.
2. A regression test fails on the old code (**RED**) and passes with the fix (**GREEN**).
3. The reviewer applies the patch in its own worktree, reproduces the checks, inspects affected callers, and runs any external review required by the task.
4. Findings get a response. Revised patches get new version names; previous handoffs are never overwritten. The writer waits for review unless explicitly replacing a version with `SUPERSEDE`.
5. Both approve the same saved version. Branch publication and pull-request review happen only when the task **requires and authorizes** them.

Documents get requirement checks, not pretend code tests. A clean automated review is useful evidence; it is not proof that the result is correct.

### Files and labels

| File or folder | Purpose |
|---|---|
| `TASK.md` | Brief, responsibilities, permissions, rules, and definition of finished. |
| `CLAUDE.md` / `CHATGPT.md` | Each agent's status on line 1, then its review messages. |
| `DRAFT.md` | Working content, where applicable. |
| Final file | The requested output format. |
| `work/` | Versioned patches, test records, and review records. |
| `work/scratch/` | Supporting logs, checkouts, and evidence; linked when needed, excluded from routine polling. |

Each agent writes only its assigned files. They read new messages and changed material instead of repeatedly loading the entire history, but read the full saved result before approval.

| Label | Meaning |
|---|---|
| `READY` | A version is ready for review. |
| `R1`, `R2`, … | A defect or unmet requirement to resolve. |
| `IDEA` | A nonblocking suggestion. |
| `SUPERSEDE` | A new version replaces one currently under review. |
| `ACCEPT <file/version> · <sha12>` | Approval of those exact saved bytes, identified by the first 12 characters of their SHA-256 hash. |

Matching approvals from both agents establish agreement. Changes reopen only affected artifacts and dependent claims. Default disagreement limits never count as approval; unresolved required decisions remain blocked.

### Manual installation

For Claude Code, run in a Bash terminal:

```bash
mkdir -p ~/.claude/skills/cc-collab && curl -fsSL https://raw.githubusercontent.com/ferasarafe/cc-collab/main/skills/cc-collab/SKILL.md -o ~/.claude/skills/cc-collab/SKILL.md
```

To make the skill available directly in Codex, save [SKILL.md](skills/cc-collab/SKILL.md) as `~/.codex/skills/cc-collab/SKILL.md`.

The `.claude-plugin/` directory contains the plugin and marketplace manifests. Full protocol: [SKILL.md](skills/cc-collab/SKILL.md).

### Where these rules came from

The current skill is **v12.0**. It was refined through a real Claude–ChatGPT collaboration, including a code review where both agents initially missed a defect. That experience shaped the independent checks, evidence records, and equal review authority. The goal is better work with less repeated review—not more paperwork for its own sake.

License: [MIT](LICENSE).
