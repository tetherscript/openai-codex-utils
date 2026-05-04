# openai-codex-utils

Public Codex utilities and skills.

## Compaction Cat

`compaction-cat` is a Codex skill that warns when a Codex session has compacted or is near the end of the context window. It uses cat-themed ASCII warning banners to make compaction warnings hard to miss. The skill warns; the user decides whether implementation continues in the same session.

The following screenshot shows a warning after compaction occurred, in this case at about 90% context window used.
<img width="1107" height="1523" alt="compacted_85_to_11perc" src="https://github.com/user-attachments/assets/7cd12deb-873f-4224-866d-4fbfd4feb2e7" />

Use it when:

- Codex is continuing from a summary instead of the full thread.
- A long thread is about to enter a risky implementation phase.
- The next step is a multi-file refactor, migration, generated-output rewrite, media workflow change, validation pass, docs update, or long debug session.
- The thread has repeated user corrections or thread-only decisions that should be captured before more stateful work.
- A computed or visible context meter is at or above the minimum warning percentage, currently 75 percent.
- You want Codex to produce a durable handoff before moving to a new thread or fresh session.

Compaction-risk estimates should use only work the user has actually requested in existing prompts. Do not count speculative future prompts, future same-thread tasks, or future work the user says will happen after closing the current thread.

When available, Compaction Cat should calculate the current thread context-window percentage from the local Codex session log before relying on practical risk signals. This is local file inspection only and does not require a query to the OpenAI or Codex model server. Use `CODEX_THREAD_ID`, find the matching JSONL session log under `%USERPROFILE%\.codex\sessions` or through `session_index.jsonl`, read the latest `event_msg` entry where `payload.type == "token_count"`, and calculate `(payload.info.last_token_usage.total_tokens / payload.info.model_context_window) * 100`. A value of 75 percent or higher satisfies the risk threshold, subject to actual-compaction priority and the once-per-session warning limit. Do not use `rate_limits.primary.used_percent` or cumulative `total_token_usage.total_tokens` as context-window occupancy.

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

Compaction Cat should sit above local workflow, build, validation, domain, and implementation instructions for any compaction or compaction-risk decision. Keep a small local fallback in `AGENTS.md` or project docs so the project remains safe when the skill is not installed. At minimum, the project instructions should say Codex must confirm when `$compaction-cat` is loaded and warn the user when `$compaction-cat` is unavailable. After actual compaction, Codex must show the `COMPACTION HAS OCCURRED` banner once for the session and recommend opening a new thread or fresh session. If actual compaction is not detected and the context-window percentage reaches the minimum warning percentage, currently 75 percent, Codex must display the `COMPACTION RISK WARNING` ASCII banner once for the session; prose alone is not enough.

## What It Does
When compaction risk is high, the skill tells Codex to warn and provide a concrete suggested continuation prompt that includes files to read, current state, commands run, validation status, constraints, and next steps. The user decides whether implementation continues in the same session. The ASCII risk warning is mandatory once the current session context-window percentage reaches the minimum warning percentage, currently 75 percent, and is shown at most once per session. If a compaction-risk warning banner appears in an interim update, the final answer and any prompt response summary or handoff must repeat the same fenced ASCII warning and suggested continuation prompt so they remain visible after progress details collapse. That same-turn repetition is part of the same warning event. Do not count speculative future prompts, future same-thread tasks, or future work the user says will happen after closing the current thread. Do not show the risk banner below 75 percent.

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

When actual compaction is detected, the skill tells Codex to warn that context truth may be corrupted and recommend a new thread or fresh session unless the user explicitly decides otherwise. The `COMPACTION HAS OCCURRED` banner is shown at most once per session. If this warning appears in an interim update, the final answer and any prompt response summary must repeat the same fenced ASCII warning. That same-turn repetition is part of the same warning event. Do not include a continuation prompt after actual compaction because summarized context may make that prompt inaccurate.

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
