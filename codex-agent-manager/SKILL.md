---
name: codex-agent-manager
description: "Let Codex manage engineering work by selecting local Waza skills, diagnosing problems, and delegating implementation to Antigravity through orchestrator-cli."
---

# Codex Agent Manager

Use Codex as the technical lead and task manager. Codex understands the request, selects the right local Waza workflow, writes an executable brief, and verifies the result. Antigravity performs the substantive implementation work. The orchestrator records dispatch, resource readiness, handoffs, retries, and evidence for work that needs more than a single bounded edit.

## Core rule

Default to delegation. Codex may edit files directly only when the change is small, local, low-risk, and cheaper to complete directly than to dispatch. Good direct-edit examples are a typo, a one-line configuration correction, or a small documentation change. Any bug with unclear cause, any multi-file change, any new behavior, and any task requiring meaningful code reasoning must be delegated to Antigravity.

Codex remains responsible for the problem statement, scope, architecture, acceptance criteria, review, and final report. A delegated task is complete only after the returned diff and verification evidence satisfy those criteria.

## Routing workflow

1. Inspect the actual workspace with `pwd` and Git status. Read applicable `AGENTS.md`, `CLAUDE.md`, and local rules before making a plan.
2. Classify the request and read the matching Waza skill from the local skills directory (normally `~/.agents/skills/<name>/SKILL.md`). Prefer the most specific installed workflow:
   - `hunt` for errors, regressions, crashes, failing tests, or broken behavior.
   - `think` for architecture, feasibility, design, and value decisions.
   - `ui` for frontend, visual, typography, and interaction work.
   - `check` for reviews, diffs, release readiness, publishing, and audits.
   - `learn` for unfamiliar domains, source bundles, or research synthesis.
   - another installed Waza skill when its description clearly matches better.
3. Apply the selected skill's diagnosis or planning process before dispatching implementation. For a bug, `hunt` must establish a likely root cause and a reproducible verification path first. Do not send Antigravity a vague request to investigate when Codex can state the observed failure and scope.
4. Decide the execution route:
   - Direct `antigravity-cli` for one bounded implementation task with a clear workspace, inputs, and verification command.
   - `orchestrator-cli` plus `antigravity-cli` for multi-step work, uncertain ownership, resource dependencies, recovery, durable handoff, or more than one implementation checkpoint.
5. Run the required resource readiness checks before sending a real task prompt. If a required CLI, model, workspace, test harness, or integration is not ready, report `blocked-resource-unavailable` with evidence instead of asking Antigravity to guess.
6. Dispatch one writer at a time. Do not run competing writing agents in the same worktree. Preserve the exact Antigravity conversation ID and the orchestrator dispatch ID.
7. Review the returned files, diff, tests, and handoff. If the result fails the acceptance criteria, send a focused follow-up to the same Antigravity conversation when it is resumable. If the session cannot be recovered, start one clearly labelled new attempt with a factual diff-based handoff.
8. Report changed files, verification output, remaining risks, and blockers. Never claim completion from an agent's assertion alone.

## Task brief for Antigravity

Every delegated prompt must include:

- objective and user-visible behavior;
- diagnosis or design decisions already made by Codex;
- explicit in-scope and out-of-scope paths;
- workspace and branch/worktree rules;
- constraints from the selected Waza skill;
- exact acceptance criteria;
- exact verification commands;
- required return fields: summary, changed files, diff or commit, verification evidence, blockers, and follow-up recommendation.

Tell Antigravity to inspect before editing when the scope is ambiguous. Keep implementation details out of the user-facing workflow unless they affect an acceptance decision.

## Bug and regression policy

For a reported bug, Codex first uses `hunt` to identify the failure path and likely root cause. It then delegates the smallest justified fix to Antigravity, including the reproduction and regression check. If verification fails, Codex updates the diagnosis and sends a targeted follow-up; it does not blindly repeat the same prompt. This keeps repeated-failure cases bounded and makes Antigravity do the code changes while Codex spends tokens on diagnosis and review. :codex-annotation{index="1"}

## Direct-edit exception

Before editing directly, state internally why dispatch overhead exceeds the implementation cost. Do not use the exception for a root-cause investigation, cross-file refactor, dependency change, migration, security-sensitive change, or work that needs a second review. Even direct edits require the appropriate verification command and a diff review.

## Failure and recovery

- A startup timeout is not proof of a stuck task. Compare progress hashes before stopping a provider process.
- Preserve provider logs and the exact native session ID on failure.
- Follow `antigravity-cli` fresh-start rules after startup or integration failures; do not repeatedly retry an unusable conversation.
- Use one control plane per orchestration run: GitHub Issues when available and authorized, otherwise `.orchestrator/` Markdown records.
- Do not create a dedicated worktree unless the user explicitly authorizes it.
- Never write credentials, tokens, private paths, or private business context into this public skill.

## Token and quality tradeoff

Delegation reduces Codex implementation tokens for medium and large tasks, but adds a small routing and review cost. Use direct edits for tiny changes and delegation for code reasoning, debugging, multi-file work, or long test loops. The objective is lower Codex consumption without sacrificing diagnosis, verification, or recovery quality.
