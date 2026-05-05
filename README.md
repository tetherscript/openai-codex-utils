# openai-codex-utils

Public Codex utilities and skills.

## Compaction Cat

`compaction-cat` is a Codex skill that warns when a Codex session has compacted or is near the end of the context window. It uses cat-themed ASCII warning banners to make compaction warnings hard to miss. The skill warns; the user decides whether implementation continues in the same session.

## What It Does
When the context window is >=75% full, compaction risk is high. The skill tells Codex to warn and provide a concrete suggested continuation prompt. The user may then continue at their own discretion.
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
You are at about 79.57% context-window usage, so a new thread is the right call before target-selection design and implementation.
Here's your handoff prompt....
```

And eventually when actual compaction is detected.
```
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
Context truth may be corrupted because I am continuing from a summary rather than the full thread log. I recommend opening a new thread before more implementation work; for this docs-only update, I will re-read the current files first and keep the edits narrowly scoped.
```

Compaction Cat calculates the current thread context-window percentage from the local Codex session log before relying on practical risk signals. This is evaluate immediately after each user-entered prompt.

The skill is located at:

```text
skills/compaction-cat
```

## Install

Copy or clone this repository, then copy the whole skill folder into your Codex skills directory. Include both `SKILL.md` and `agents/openai.yaml`.

Codex normally loads skills from:

```text
$CODEX_HOME/skills
```

If `CODEX_HOME` is not set, use:

```text
~/.codex/skills
```

On Windows, that usually resolves to:

```text
%USERPROFILE%\.codex\skills
```

The final installed folder should look like:

```text
<your Codex skills directory>/compaction-cat/SKILL.md
<your Codex skills directory>/compaction-cat/agents/openai.yaml
```

After installing, prefer the `AGENTS.md` entry below for automatic project-level use. You can also invoke the skill explicitly when needed:

```text
Use $compaction-cat to decide whether this long Codex thread needs a durable handoff before more implementation work.
```

Codex may also use the skill implicitly when its metadata matches the current task.

## Recommended AGENTS.md Entry

For strongest protection, mention Compaction Cat near the top of your project's `AGENTS.md`, before project-specific workflow instructions. Keep this as a short entry-point rule and leave the detailed workflow in the installed skill:

```text
Use $compaction-cat for context compaction, compaction-risk warnings, durable handoffs, and recovery after summary-based continuation. Follow it before project-specific workflow instructions whenever compaction or compaction risk is detected.
When $compaction-cat is used, confirm it visibly in the same response with: Loaded skill: $compaction-cat.
Do not use a project-local fallback for compaction handling. If $compaction-cat is unavailable, report that the required skill is unavailable and do not continue compaction-sensitive work until it is available.
```

## License

See `LICENSE`.
