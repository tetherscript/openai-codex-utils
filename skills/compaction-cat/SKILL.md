---
name: compaction-cat
description: Prevent unsafe Codex work across context compaction and long-thread context risk with cat-themed ASCII warning banners. Use when Codex detects summary-based continuation, compressed thread memory, context compaction, long or context-heavy threads, risky next phases, multi-file refactors, migrations, generated-output rewrites, extended debug sessions, handoff planning, or user questions about whether a new thread is prudent before more implementation work.
---

# Compaction Cat

Use this skill to warn clearly when a Codex thread or session has compacted or is likely to compact. Favor durable workspace state and current files over summarized conversation memory. The skill warns; the user decides whether implementation continues in the same session.

When this skill is available and used, confirm it visibly in the same response:

```text
Loaded skill: $compaction-cat.
```

For actual compaction, keep the `COMPACTION HAS OCCURRED` banner first, then include the loaded-skill confirmation after the banner.

## Decision Rules

1. Actual compaction has priority over compaction risk. If actual compaction or summary-based continuation is detected, handle only `COMPACTION HAS OCCURRED` for that turn.
2. Show `COMPACTION HAS OCCURRED` once for the current session, and only when actual compaction or summary-based continuation is detected.
3. If actual compaction is not detected, calculate the current session context-window usage before evaluating compaction risk.
4. Show `COMPACTION RISK WARNING` once for the current session, and only when current session context-window usage is known to be at least the minimum warning percentage, currently 75 percent.
5. After either warning, the user decides whether implementation continues in the same session, moves to a new thread, or pauses.
6. Before recommending a new thread for compaction risk, capture durable state when the current thread still has enough context to do so accurately.
7. Do not trust compressed memory for current code state, user corrections, or partially completed edits.
8. In the new thread, re-read the relevant files and instructions before continuing work.
9. Keep warnings and handoffs concrete. Avoid generic advice that leaves the next thread guessing.
10. When the local Codex session log is available, calculate the current thread context-window usage locally from the latest `token_count` event before relying on visible UI meters or practical risk signals. Do not query the OpenAI or Codex model server for this calculation.
11. Treat each new user prompt as a silent context-window checkpoint. At the start of each new assistant turn, before any substantive work, read the latest local `token_count` event when available and calculate the current context-window percentage.
12. If that prompt-boundary checkpoint computes context-window usage at 75 percent or higher, and `COMPACTION RISK WARNING` has not already been shown in the current session, show the risk banner immediately before doing any other work.
13. Routine context-window checks are silent. Do not announce that you are looking up the thread id, session log, token-count event, or current percentage unless the user explicitly asks or a warning must be shown.
14. Do not show the risk banner below 75 percent. Practical risk signals may justify concise advice, but they do not override the minimum percentage threshold.
15. Calculate potential token usage from work the user has actually requested in existing prompts. Do not count speculative future work, including work the user says they may request later in the same thread or after closing the thread, as a current-thread compaction risk signal.
16. If the user says they plan to start a large task in a future prompt or a new thread, provide a normal concise handoff or startup prompt if useful. Do not show the compaction-risk banner for that future task unless the current session context-window usage is at or above the minimum warning percentage.
17. If a `COMPACTION RISK WARNING` banner is shown in an interim update, repeat the same fenced ASCII banner and suggested continuation prompt in the final answer and in any prompt response summary for that turn.
18. If a `COMPACTION HAS OCCURRED` banner is shown in an interim update, repeat the same fenced ASCII banner in the final answer and in any prompt response summary for that turn, but do not include a continuation prompt.

## Handoff Prompts Are Not Compaction

Treat user-authored handoff, resume, continuation, current-state, or startup prompts as ordinary user input unless the active system context separately says the session itself was compacted or the assistant can only see a compressed conversation summary. A user can paste a detailed handoff into a fresh, uncompacted thread; that handoff may be useful context, but it is not evidence that `COMPACTION HAS OCCURRED`.

When a user-authored handoff is present without actual compaction:

1. Use durable files and current workspace state as source of truth.
2. Re-read relevant docs and source files before implementation.
3. Do not show the `COMPACTION HAS OCCURRED` banner.
4. Evaluate normal compaction risk separately, and show `COMPACTION RISK WARNING` only when the current context-window usage is known to be at least 75 percent.

## Per-Session Warning Limits

Each warning type is shown at most once per Codex session:

1. Show `COMPACTION RISK WARNING` only the first time the current session context-window usage is known to be 75 percent or higher.
2. After `COMPACTION RISK WARNING` has been shown once, do not show that banner again in the same session. Continue with concise prose if more compaction-risk discussion is needed.
3. Show `COMPACTION HAS OCCURRED` only the first time actual compaction or summary-based continuation is detected in the current session.
4. After `COMPACTION HAS OCCURRED` has been shown once, do not show that banner again in the same session. Continue with concise prose if more compaction discussion is needed.
5. These limits apply separately. A session may show no banners, only the risk banner, only the occurred banner, or both banners once each.
6. Repeating a banner in the final answer or prompt response summary for the same turn is part of the same warning event. It does not count as showing the warning again.

## Prompt Response Summaries

If a warning is shown during a turn, preserve it in the response summary for that same prompt:

1. For `COMPACTION RISK WARNING`, include the same fenced ASCII banner and the suggested continuation prompt.
2. For `COMPACTION HAS OCCURRED`, include only the same fenced ASCII banner.
3. Never include a suggested continuation prompt with `COMPACTION HAS OCCURRED`; actual compaction means summarized context may have corrupted context truth.

## Silent Context Checks

Context-window measurement should not add routine chatter to the thread:

0. Treat each new user prompt as a silent checkpoint before any substantive assistant work.
1. Do not say that the local thread id is visible.
2. Do not say that you are looking up the matching session log.
3. Do not say that you are reading or checking the `token_count` event.
4. Do not say that you will proceed without a banner if the value is below 75 percent.
5. If the computed percentage is below 75 percent and actual compaction is not detected, continue normally with no compaction-related message.
6. Mention the computed percentage only when the user asks for it, when a warning is shown, or when lookup failure directly affects a user-requested compaction-status answer.

## Compaction Detected

Treat any of these as actual compaction:

1. The active system or developer context says the conversation was compacted, summarized, resumed, or continued from a summary.
2. The assistant sees only a system-provided summary of prior work instead of the full prior thread.
3. The assistant cannot inspect the latest detailed conversation turns that led to the current state because they were replaced by compressed context.

Do not treat these as actual compaction by themselves:

1. A user-authored continuation prompt.
2. A pasted handoff.
3. A resume checklist.
4. A current-state summary written by the user.
5. A request to continue work from a named commit, dirty worktree, active plan, or validation checkpoint.

When actual compaction is detected and the `COMPACTION HAS OCCURRED` banner has not already been shown in the current session, begin the next assistant message with this fenced text block:

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
This thread has compacted. Context truth may be corrupted because I am continuing from a summary instead of the full thread. I recommend opening a new thread or fresh session before more implementation work, but you decide whether to continue here.
```

Do not generate a continuation prompt after actual compaction. Because context truth may be corrupted, an accurate continuation prompt cannot be guaranteed. If the user chooses to continue implementation in the compacted session, rely on durable docs and current workspace files rather than summarized conversation memory.

If this warning is shown during a turn, repeat the same fenced ASCII banner in the final answer and in any prompt response summary. Do not include a continuation prompt with the occurred banner.

## Context Percentage From Current Local Session Log

When available, get the current session context-window percentage from the local Codex session JSONL for the current thread. This calculation is local file inspection only; it does not require or justify a query to the OpenAI or Codex model server.

The source of truth is the latest `token_count` event in the current thread's matching session log under `%USERPROFILE%\.codex\sessions`. The current thread is identified by `CODEX_THREAD_ID`, and the matching log is usually the JSONL filename containing that id. If the filename is not enough, use `session_index.jsonl` only to locate the current thread's session log.

1. Get the current thread id from the `CODEX_THREAD_ID` environment variable.
2. Find the current thread's matching local session log under `%USERPROFILE%\.codex\sessions`.
3. Prefer a JSONL filename that contains the thread id. If needed, use `session_index.jsonl` to map ids to thread names or log paths, then choose the newest matching JSONL session log for the current thread.
4. Read the latest JSONL entry in that session log where `type == "event_msg"` and `payload.type == "token_count"`.
5. Read the current context token count from `payload.info.last_token_usage.total_tokens`.
6. Read the model context-window size from `payload.info.model_context_window`.
7. Calculate `context_window_used_percent` as:

```text
(payload.info.last_token_usage.total_tokens / payload.info.model_context_window) * 100
```

8. Use this computed percentage as the current thread context-window usage.
9. Apply the `Compaction Risk` rules to this computed percentage. A value of 75 percent or higher satisfies the percentage threshold, but the risk banner is still subject to actual-compaction priority and the once-per-session limit.
10. Do not use `rate_limits.primary.used_percent`, cumulative `total_token_usage.total_tokens`, or an estimated future prompt size for this value; those are not current context-window occupancy.
11. If `CODEX_THREAD_ID`, the matching session log, or a `token_count` event is unavailable, no local computed percentage is available. Fall back to a visible UI meter or a user-provided meter for the risk threshold. Practical risk signals alone may justify concise advice, but they do not trigger the risk banner.

Example:

```text
last_token_usage.total_tokens = 55462
model_context_window = 258400
context_window_used_percent = 21.46 percent
```

This example is below 75 percent, so the context percentage alone does not require a compaction-risk warning.

## Compaction Risk

Evaluate compaction risk only when actual compaction is not detected in the current turn. Show a risk warning only when the current session context-window usage is known to be at or above the minimum warning percentage, currently 75 percent, and the `COMPACTION RISK WARNING` banner has not already been shown in the current session. Base any suggested continuation prompt on work the user has actually requested in existing prompts, not speculative future prompts or tasks the user says will happen in a later thread.

The percentage source can be:

1. The local session-log calculation from `payload.info.last_token_usage.total_tokens` and `payload.info.model_context_window`.
2. A visible Codex UI context meter.
3. A user-provided context-window percentage.

When the risk banner is shown, explain that opening a new thread is recommended, but the user decides whether implementation continues in the current session.

At or above 75 percent, stateful phases are riskier. Examples include:

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

If no exact context percentage is available from the local session log, the UI, or the user, do not show the risk banner. Use concise prose if practical risk signals matter:

1. The thread has accumulated many file reads, tool outputs, build or validation logs, debugging steps, or implementation phases.
2. The next step would be hard to review or roll back if recent conversation turns were lost.
3. The assistant has already said a new thread or fresh session would be prudent.
4. Important constraints or decisions still exist only in the conversation.

If a computed context percentage, visible context percentage, or token meter is available:

1. Use 75 percent as the minimum warning percentage because it leaves room to capture durable state before hard compaction.
2. Do not show the risk banner below 75 percent.
3. At or above 85 percent, the recommendation to open a new thread should be stronger, but the banner is still shown only once per session.
4. Do not wait for a known failure point such as a prior observed percentage unless the runtime provides a documented hard limit.
5. Do not count future prompts, future tasks, or work the user says will happen after closing the current thread when estimating current-thread token risk.

When showing the risk warning, show this fenced text block:

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

Then include a suggested continuation prompt in a fenced `text` code block. Use this shape and fill in the current workspace, files, and task:

```text
Start a new Codex thread or fresh session in <absolute workspace path>.

Read first:
1. <project instructions>
2. <durable handoff, active plan, or relevant domain docs>

Task:
<one sentence describing the next implementation step>

Current state:
1. Files changed: <paths or none>
2. Commands run: <commands and results>
3. Validation status: <passed, failed, not run, or blocked>
4. Decisions made: <concrete decisions>
5. Known risks: <specific risks>

Constraints:
1. Do not trust summarized conversation memory.
2. Re-read current files before editing.
```

After the warning, the user decides whether to continue implementation in the same session or move to a new thread. Do not show the risk banner again in the same session; use concise prose if more risk discussion is needed.

If this warning is shown during a turn, repeat the same fenced ASCII banner and the suggested continuation prompt in the final answer and in any prompt response summary or handoff.

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

1. Treating a warning as an automatic stop instead of letting the user decide whether implementation continues.
2. Treating a summary as equivalent to the full thread.
3. Trusting remembered file contents instead of re-reading files.
4. Losing recent user corrections or constraints.
5. Showing `COMPACTION RISK WARNING` below the minimum warning percentage.
6. Showing either warning banner more than once in the same session.
7. Giving a continuation prompt after actual compaction, when context truth may already be corrupted.
8. Giving a handoff without exact files, commands, validation status, and constraints.
9. Showing a compaction-risk warning only in an interim update and omitting the banner and continuation prompt from the final answer or prompt response summary.
10. Showing a compaction-occurred warning only in an interim update and omitting the banner from the final answer or prompt response summary.
11. Treating speculative future prompts or future-thread work as current-thread compaction risk.
12. Announcing routine context-window lookup details when no warning is needed and the user did not ask for the percentage.
13. Treating a user-authored handoff or continuation prompt as proof that the current assistant session has compacted.
