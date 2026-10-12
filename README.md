# cc-collab

### Tired of copy-pasting between Claude and ChatGPT?

“Here's what Claude said.” Copy. Paste. “Here's what ChatGPT thinks.” Copy. Paste. Somehow, **you became the messenger between two of the world's best AI agents.**

**cc-collab lets them talk through shared Markdown files instead.** They read each other's work, challenge ideas, make improvements, and keep going until both approve the same result. No more carrying messages between chats.

Set them up, give them a task, and go back to TikTok doomscrolling while Claude and ChatGPT do the work. They'll flag decisions that need you—you can check their progress whenever you like. Keep both sessions active so the conversation can continue.

**A proposal, a business plan, research, or a code fix:** one writes, the other checks, and both have an equal say.

cc-collab is a free **skill**—a set of instructions for the AI tools you already use. Bring your own Claude and ChatGPT/Codex sessions, give both access to the same folder, and let them collaborate.

[Get started](#get-started) · [See examples](#what-can-you-use-it-for) · [Read the rules](skills/cc-collab/SKILL.md)

## Why use two assistants?

A second answer is useful. A second assistant that can inspect the first assistant's work, question its assumptions, and request corrections can be more useful.

cc-collab gives that exchange a structure:

- **A second perspective.** The reviewer looks for missing requirements, weak reasoning, and mistakes.
- **Less manual coordination.** The assistants exchange feedback through files, so you do not have to copy every response between chats.
- **A clear finish.** Both approve the exact same version, rather than agreeing in general while looking at different drafts.
- **Visible checks.** You can see what was reviewed, what was tested, and what still needs your decision.

This is designed to improve the result, not guarantee that it is correct. Two assistants can share the same blind spot. It also uses two AI sessions, so it can cost more than asking one assistant. Time and cost savings have not been measured.

## What can you use it for?

| If you need… | The assistants can work together to… |
|---|---|
| A client proposal | Prepare the proposal, then check it against your brief, pricing, and commitments. |
| A business plan or decision memo | Develop the recommendation, challenge its assumptions, and identify missing information. |
| A research summary | Organize the findings and check whether the evidence supports the conclusions. |
| A product specification | Describe the intended behavior and look for gaps or conflicting requirements. |
| A code fix | Investigate the cause, make a focused change, and independently check the tests and affected code. |

It is most useful when the work deserves a second review. A quick rewrite or simple question may not need two assistants.

## How it works

**Your brief → first draft → independent review → improvements → approval from both**

1. **You describe the result you want.** Include the audience, useful source material, and anything the assistants must preserve or avoid.
2. **The assistants agree how to work.** They check access, divide writing responsibilities, and ask any essential questions together.
3. **They exchange drafts and feedback.** One writes; the other reviews. Neither can overrule the other just because of its role.
4. **They improve and check the result.** Code gets code tests. Documents are checked against your requirements.
5. **Both approve the same saved version.** You receive the result with any remaining limitations or actions clearly stated.

You step in when a decision needs your judgment. If a required decision remains unresolved, the work is marked blocked, not finished. Sending, publishing, or merging work needs your permission.

## What you need

- **Two active AI sessions:** Claude Code and Codex, or another ChatGPT environment that can read and write the same local folder.
- **One shared folder:** both assistants must be able to save files there and read each other's updates.
- **Access to any tools your task needs:** for example, your code repository or a testing tool.

**Uploading a file to an ordinary ChatGPT conversation is not enough.** This workflow needs continuing read/write access to the shared folder. Both assistants also need to stay active while waiting, or have a verified way to wake up and check for updates.

The skill does not open the second session or provide those capabilities for you. If access or monitoring does not work, the assistants should stop and explain the problem.

## Get started

### 1. Install the skill for Claude Code

Inside Claude Code, run:

```text
/plugin marketplace add ferasarafe/cc-collab
/plugin install cc-collab@ferasarafe
```

Alternatively, install it as a personal skill from a Bash terminal:

```bash
mkdir -p ~/.claude/skills/cc-collab && curl -fsSL https://raw.githubusercontent.com/ferasarafe/cc-collab/main/skills/cc-collab/SKILL.md -o ~/.claude/skills/cc-collab/SKILL.md
```

Codex does not need a separate installation to join: Claude puts the task's rules in the shared folder. To make the skill available directly in Codex too, save [SKILL.md](skills/cc-collab/SKILL.md) as `~/.codex/skills/cc-collab/SKILL.md`.

### 2. Give Claude your brief

Create a folder for the task, open Claude Code with access to it, and replace the brackets below:

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

For example: “I want a one-page proposal in Word for a restaurant owner, based on these meeting notes. It must preserve the agreed price and must not invent additional services.”

### 3. Start the second assistant

Open Codex, or a compatible ChatGPT environment, with access to the **same folder**. Paste the startup message Claude gives you.

This is the one-time handoff. After that, the assistants exchange their work through the folder; you do not need to relay their reviews.

### 4. Follow the progress

Read either assistant's latest status or the first line of its conversation file. Answer questions marked `OWNER` or `OWNER DECIDES` when needed.

When both approve the same result, they mark it `DONE` and tell you what is ready. Review any stated limitations before using or publishing it.

## What the labels mean

| Label you may see | Plain meaning |
|---|---|
| `READY` | This version is ready for the other assistant to review. |
| `R1`, `R2`, … | A numbered problem or unmet requirement to resolve. |
| `IDEA` | An optional suggestion, not a required correction. |
| `SUPERSEDE` | A newer version replaces the previous review version. |
| `ACCEPT` | The assistant approves this exact saved version. |
| `sha12` | A short file fingerprint used to check that both approved the same version. |
| `OWNER DECIDES` | A question that needs your judgment. |
| `SETUP BLOCKED` | Required access or a working way to monitor updates is missing. |
| `DONE` | Both approved the required outputs and the required checks are resolved. |

## Where the work is saved

| File or folder | What it contains |
|---|---|
| `TASK.md` | Your brief, responsibilities, permissions, and definition of finished. |
| `CLAUDE.md` | Claude's status and review messages. |
| `CHATGPT.md` | ChatGPT/Codex's status and review messages. |
| `DRAFT.md` | The working draft, where applicable. |
| Your final file | The requested result, such as a document or specification. |
| `work/` | Code changes and records of what was checked, when needed. |
| `work/scratch/` | Supporting evidence and temporary working material. |

Each assistant writes only its assigned files. They keep supporting evidence available without rereading the whole folder on every check.

## For developers: how code review works

The assistants investigate independently and agree on the cause and smallest fix before coding. The reviewer determines the expected behavior before relying on the writer's test assertions.

For each code change:

1. The writer supplies one versioned patch and a test record: baseline commit, file fingerprint, commands, results, and verification limits.
2. A regression test fails on the old code (**RED**) and passes after the fix (**GREEN**).
3. The reviewer applies the patch independently, reproduces the checks, inspects affected callers, and runs any external review required by the task.
4. The writer answers every finding. Replacements get new version names; old handoffs are not overwritten.
5. Both approve the same version. Branch publication and pull-request review happen only when the task **requires and authorizes** them.

Passing tests or a clean automated review is evidence, not proof of correctness. Document tasks use requirement checks rather than artificial RED/GREEN tests.

## About this version

The current skill is **v12.0**. It was refined through a real Claude–ChatGPT collaboration, including a code review where both assistants initially missed a defect. The rules emphasize independent reasoning, clear evidence, equal authority, and less repeated review work.

The complete instructions are in [SKILL.md](skills/cc-collab/SKILL.md). The `.claude-plugin/` folder contains the files Claude Code uses to install the plugin.

License: [MIT](LICENSE).
