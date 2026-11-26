# claude-skills-boilerplate

A starter set of [Claude Code skills](https://docs.claude.com/en/docs/claude-code/skills) for planning, reviewing, and shipping pull requests.

## Use it

Click **Use this template** on GitHub, or copy the skills into an existing project:

```bash
npx degit rmdelossantos/claude-skills-boilerplate/.claude/skills .claude/skills
```

Skills in `.claude/skills/` are picked up automatically. Run one with `/<name>`, e.g. `/ship add rate limiting to the API`.

## Skills

Names follow `<stage>-<action>`, so each stage sorts together.

| Skill | What it does |
|---|---|
| **`ship`** | End-to-end pipeline: reframe → plan → implement in a worktree → review → tidy → CI green → handover. Calls most of the skills below. |
| `plan-draft` | Turns a rough idea into a Markdown plan, deciding instead of interviewing. |
| `plan-polish` | Hardens a plan through a subagent review loop (up to 7 rounds). |
| `plan-triangulate` | Gets an independent second plan and merges the best of both. |
| `review-code` | Reviews a diff or PR; posts findings as PR review threads. |
| `review-loop` | Loops `review-code` → fix → reply → resolve on a PR, up to 10 passes. |
| `review-risk` | Grades a PR or plan on a 5-level review-effort scale, "glance" to "war room". |
| `pr-autofix` | Drives a PR to merge-ready: fixes CI, addresses review threads, requests Copilot review. |
| `pr-declutter` | Minimizes noisy PR comments while keeping the signal. |
| `pr-walkthrough` | Writes a reviewer guide: call graphs, chaptered diffs, sequence diagrams. |
| `tidy-prose` | Finds and fixes AI-sounding prose. |
| `tidy-branch` | Cuts low-value comments and weak tests added on the branch. |

## How they connect

```
ship ─┬─ plan-draft ── review-risk
      ├─ plan-polish
      ├─ review-loop ── review-code
      ├─ pr-autofix ─── review-code
      ├─ tidy-branch, tidy-prose
      ├─ pr-declutter
      └─ pr-walkthrough
plan-triangulate ── plan-draft
```

`review-code` and `review-loop` reuse scripts from `pr-autofix/scripts/`, so keep those skills together.

## Requirements

- [`gh`](https://cli.github.com/) authenticated, and `jq`, for the PR skills.
- Optional skills referenced but not included: `/pr-description`, `/skimmable`, `/impact-analysis`, `/worktrunk`. Skills fall back gracefully when they're missing.
