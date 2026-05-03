---
name: compaction-cat
description: Prevent unsafe Codex work across context compaction and long-thread context risk with cat-themed ASCII warning banners. Use when Codex detects summary-based continuation, compressed thread memory, context compaction, long or context-heavy threads, risky next phases, multi-file refactors, migrations, generated-output rewrites, extended debug sessions, handoff planning, or user questions about whether a new thread is prudent before more implementation work.
---

# Compaction Cat

Use this skill to preserve task correctness when a Codex thread or session has compacted or is likely to compact during risky work. Favor durable workspace state and current files over summarized conversation memory.

When this skill is available and used, confirm it visibly in the same response:

```text
Loaded skill: $compaction-cat.
```

For actual compaction, keep the `COMPACTION HAS OCCURRED` banner first, then include the loaded-skill confirmation after the banner.

## Decision Rules

1. If actual compaction or summary-based continuation is detected, stop implementation work in the current thread.
2. If the next phase is risky and the current thread or session is already long or context-heavy, warn before starting and recommend a new thread or fresh session.
3. Before recommending a new thread, capture durable state when the current thread still has enough context to do so accurately.
4. Do not trust compressed memory for current code state, user corrections, or partially completed edits.
5. In the new thread, re-read the relevant files and instructions before continuing work.
6. Keep warnings and handoffs concrete. Avoid generic advice that leaves the next thread guessing.
7. Treat compaction risk as practical, not tied to a single exact percentage. Warn early enough to preserve decisions before the hard compaction boundary.

## Compaction Detected

Treat any of these as actual compaction:

1. The active context says the conversation was compacted, summarized, resumed, or continued from a summary.
2. The assistant sees only a summary of prior work instead of the full prior thread.
3. The assistant cannot inspect the latest detailed conversation turns that led to the current state.

When actual compaction is detected, begin the next assistant message with this fenced text block:

```text
############################################################
#                                                          #
#              COMPACTION HAS OCCURRED                     #
#                                                          #
#   I AM CONTINUING FROM A SUMMARY, NOT FULL THREAD LOG.   #
#                                                          #
#        /\_/\                                             #
#       ( o.o )     CONTEXT PURITY WARNING                 #
#        > ^ <                                             #
#                                                          #
############################################################
```

Then say:

```text
This thread has compacted. Please open a new thread or fresh session before we continue implementation work. I will rely on durable docs, active plans, and the current workspace files from there.
```

Do not continue implementation in the compacted thread or session. Only answer short clarifying questions or help the user prepare a concise startup prompt for a new thread or fresh session.

## Compaction Risk

Show a risk warning before starting a new phase when both are true:

1. The thread or session is already long, context-heavy, or the assistant has already said a new thread or fresh session would be prudent.
2. The next step is broad, stateful, or hard to review if compaction occurs mid-work.

Risky phases include:

1. Multi-file refactors.
2. Generated-output rewrites.
3. Migrations.
4. Large test-harness, build-system, or documentation-generation changes.
5. Long debug sessions that may require many tool calls.
6. Work where rollback or review depends on subtle prior decisions.
7. Long-running multi-step work that combines code changes with docs, active plans, generated output, or validation.
8. Generated output or media workflow changes, including help output, screenshots, images, installer assets, or validation artifacts.
9. Repeated user corrections, constraints, or decisions that are important and not yet captured durably.
10. Another implementation, validation, generated-output, or docs phase after a major phase appears complete.

If no exact context percentage is visible, use practical risk signals:

1. The thread has accumulated many file reads, tool outputs, build or validation logs, debugging steps, or implementation phases.
2. The next step would be hard to review or roll back if recent conversation turns were lost.
3. The assistant has already said a new thread or fresh session would be prudent.
4. Important constraints or decisions still exist only in the conversation.

If a context percentage or token meter is visible:

1. Use 75 percent as the conservative warning point because it leaves room to capture durable state before hard compaction.
2. At or above 80 percent, warn before any non-trivial implementation, validation, generated-output, media, or docs phase.
3. Do not wait for a known failure point such as a prior observed percentage unless the runtime provides a documented hard limit.

Before starting that phase, show this fenced text block:

```text
############################################################
#                                                          #
#              COMPACTION RISK WARNING                     #
#                                                          #
#   NEXT STEP IS LARGE ENOUGH TO RISK MID-WORK COMPACTION. #
#                                                          #
#        /\_/\       RECOMMENDED: NEW THREAD NOW           #
#       ( -.- )      I CAN CAPTURE STATE FIRST             #
#        > ^ <                                             #
#                                                          #
############################################################
```

Then stop before implementation unless the user asks only for a small docs-only update, a short answer, or a durable handoff.

## Durable Handoff

When recommending a new thread or fresh session, provide a ready-to-use startup prompt. Include only facts that are durable or recently verified from files, commands, or explicit user messages.

Use this structure:

```text
Start a new Codex thread or fresh session in <absolute workspace path>.

Read first:
- <file or instruction source>
- <file or instruction source>

Task:
<one sentence describing the next implementation step>

Current state:
- Files changed: <paths or none>
- Commands run: <commands and results>
- Validation status: <passed, failed, not run, or blocked>
- Decisions made: <concrete decisions>
- Known risks: <specific risks>

Constraints:
- <repo or user constraints>
- Do not trust summarized conversation memory.
- Re-read current files before editing.
```

If the project has durable docs, active plans, issue trackers, or bookmark files, update the appropriate durable location before giving the handoff when the user has asked for implementation work and the state would otherwise be lost. If the project has no durable handoff convention, include the complete handoff in the response.

## New Thread Or Fresh Session Resume

In the new thread or fresh session:

1. Read project instructions first.
2. Read any durable handoff docs or active plans.
3. Re-read current source files before editing.
4. Verify the latest user request in the new thread.
5. Continue only from current workspace state, not from memory of the prior thread.

## Common Mistakes

1. Continuing implementation after detecting actual compaction.
2. Treating a summary as equivalent to the full thread.
3. Trusting remembered file contents instead of re-reading files.
4. Losing recent user corrections or constraints.
5. Starting a long refactor after warning about context risk.
6. Giving a handoff without exact files, commands, validation status, and constraints.
