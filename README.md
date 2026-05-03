# openai-codex-utils

Public Codex utilities and skills.

## Compaction Cat

`compaction-cat` is a Codex skill that helps prevent unsafe implementation work after context compaction or near the end of a long, context-heavy thread. It uses cat-themed ASCII warning banners to make compaction warnings hard to miss.

Use it when:

- Codex is continuing from a summary instead of the full thread.
- A long thread is about to enter a risky implementation phase.
- The next step is a multi-file refactor, migration, generated-output rewrite, media workflow change, validation pass, docs update, or long debug session.
- The thread has repeated user corrections or thread-only decisions that should be captured before more stateful work.
- A visible context meter is at or above 75 percent, or no meter is visible but practical risk signals show the thread is already context-heavy.
- You want Codex to produce a durable handoff before moving to a new thread or fresh session.

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

For strongest protection, make Compaction Cat the top-level rule in your project's `AGENTS.md`, before project-specific workflow instructions:

```text
Use $compaction-cat as the top-level rule for context compaction, compaction-risk warnings, and durable handoffs. Follow it before project-specific workflow instructions whenever compaction or compaction risk is detected.
When $compaction-cat is available and used, confirm it visibly in the same response with: Loaded skill: $compaction-cat.
If $compaction-cat is unavailable, warn the user explicitly in the same response before using the local fallback workflow.
```

Compaction Cat should sit above local workflow, build, validation, domain, and implementation instructions for any compaction or compaction-risk decision. Keep a small local fallback in `AGENTS.md` or project docs so the project remains safe when the skill is not installed. At minimum, the project instructions should say Codex must confirm when `$compaction-cat` is loaded and warn the user when `$compaction-cat` is unavailable. After actual compaction, Codex must show the `COMPACTION HAS OCCURRED` banner, stop implementation work, and ask the user to open a new thread or fresh session.

## What It Does

When actual compaction is detected, the skill tells Codex to pause implementation work, recommend a new thread or fresh session, and resume from durable project state unless the user explicitly decides otherwise.

When compaction risk is high, the skill tells Codex to warn before starting risky work and provide a concrete handoff that includes files to read, current state, commands run, validation status, constraints, and next steps. The warning is based on practical risk signals, not one exact percentage. If a context percentage or token meter is visible, 75 percent is the conservative warning point because it leaves room to capture durable state before hard compaction; at or above 80 percent, warn before any non-trivial implementation, validation, generated-output, media, or docs phase.

## COMPACTION RISK WARNING Example

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

## COMPACTION HAS OCCURRED Example

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

## License

See `LICENSE`.
