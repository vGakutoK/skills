---
name: git-assist
description: Safe Git and GitHub workflow assistance for app development and maintenance. Use when the user invokes /git-assist or asks for Git work preparation, pre-work checks, commit-before review, commit message creation, commit message suggestions, PR drafting, PR creation support, or safe handling of local changes across solo or team development.
---

# Git Assist

Use this skill to support safe, organized Git/GitHub operations without widening the scope beyond what the user asked for.

## Mode Selection

When the user invokes `/git-assist` without specifying a mode, first show this menu and wait for the user to choose:

1. 作業前チェック
2. コミット前レビュー
3. コミットメッセージ作成
4. PR作成支援

If the user explicitly asks for a mode, skip the menu and start that mode directly. Treat requests such as "作業前チェック", "コミット前レビューして", "コミットメッセージ作成", and "PR作成支援" as explicit mode selection.

Ask which mode to use only when the request cannot be reasonably classified.

## Global Safety Rules

- Prioritize safety checks.
- Keep the scope narrow and practical.
- Do not run destructive or history-changing commands automatically, including `git reset --hard`, `git push --force`, `git rebase`, and `git stash drop`.
- Run `git commit`, `git push`, PR creation commands, `git rebase`, or similar state-changing operations only when the user explicitly permits that specific action.
- Prefer summarizing information useful for review and change confirmation over listing implementation details exhaustively.
- Keep output concise and practical.
- Suggest test perspectives or test results only when the user explicitly asks for them.
- Prefer output that can be used directly in real work.
- Prioritize copy-and-paste-friendly output.
- Avoid overusing Markdown code fences.
- Reduce unnecessary decoration and sample-like wording.
- Use lightweight Conventional Commits-style commit message suggestions when proposing commit messages:
  - `feat: ...` for feature additions
  - `fix: ...` for bug fixes
  - `refactor: ...` for behavior-preserving cleanup
  - `docs: ...` for documentation changes
  - `test: ...` for test additions or updates
  - `chore: ...` for other maintenance

## 作業前チェック

Focus only on the current Git state.

Run:

```bash
git branch --show-current
git status --short
git diff --stat
git diff --name-only
git log --oneline -n 1
```

Then report:

- Current branch.
- Whether the branch appears protected or shared, especially `main`, `master`, or `develop`.
- Whether there are uncommitted changes or untracked files.
- Whether a diff exists and which files are changed.
- The most recent commit.
- Whether the current work seems likely to mix with or continue the previous commit in a confusing way.
- If uncommitted changes exist, suggest safe choices before starting new work, such as commit, stash, or creating a separate branch.

Do not review detailed diff contents, confirm task intent, or suggest test perspectives unless the user explicitly asks.

## コミット前レビュー

Focus on the current Git state and diff before commit.

Run:

```bash
git status --short
git diff --stat
git diff --name-only
```

If staged changes exist, inspect them as needed:

```bash
git diff --cached
```

If more context is needed, inspect unstaged content as needed:

```bash
git diff
```

Then report:

- Changed and untracked files.
- Change volume summary.
- Concise summary of the change content.
- Possible accidental changes, unrelated changes, or unnecessarily large changes.
- Whether the work should be split into multiple commits.
- Lightweight Conventional Commits-style commit message suggestions.

Do not run `git commit` unless the user explicitly permits it.

## コミットメッセージ作成

Focus on summarizing the change and generating practical, readable commit message suggestions.

Run:

```bash
git status --short
git diff --stat
```

If needed to understand the change, inspect:

```bash
git diff --name-only
git diff
```

If a project-specific commit style may exist, inspect recent commit messages as needed:

```bash
git log --oneline -n 10
```

Then report:

- Concise summary of the change.
- One or more lightweight Conventional Commits-style commit message suggestions.
- The reason for the recommended prefix when useful.

Commit message policy:

- Use Conventional Commits-style format by default.
- Use English prefixes such as `feat`, `fix`, `refactor`, `docs`, `test`, and `chore`.
- Write the commit description in concise Japanese by default.
- Prefer wording that communicates the change purpose or user-visible change over implementation details.
- If project-specific commit rules or recent commit history show a clear style, prioritize that style for format, language, and granularity.
- Keep commit messages short and readable.

Commit message output rules:

- Do not wrap commit messages in Markdown code fences.
- Output commit messages in a directly copyable format.
- Avoid excessive explanatory text.
- Prioritize practical use and copyability.

Do not run `git commit` unless the user explicitly permits it.

## PR作成支援

Focus on organizing the whole PR for review.

Run:

```bash
git branch --show-current
```

Infer the base branch from local branches and repository conventions, preferring `main`, then `master`, then `develop` when appropriate. Confirm the inferred base if uncertain.

Inspect the PR-sized diff:

```bash
git diff --stat <base-branch>...HEAD
git diff --name-only <base-branch>...HEAD
```

If needed for an accurate PR summary, inspect:

```bash
git diff <base-branch>...HEAD
```

Then prepare:

- PR title suggestion.
- PR body suggestion focused on review needs rather than implementation trivia.
- Key review points.
- Review method only when it materially helps reviewers confirm the change.

PR title output rules:

- Output the PR title in a directly copyable format.
- Do not add unnecessary decoration or explanatory text around the PR title.
- You may wrap the PR title in a Markdown code fence when that makes it easier to copy.
- Prioritize practical use and copyability.

PR body output rules:

- Output the PR body as raw Markdown text.
- Generate Markdown that can be pasted directly into GitHub.
- Prioritize copyable raw Markdown over Markdown-rendered presentation in the Codex/ChatGPT UI.
- You may wrap the PR body in a Markdown code fence when that makes the raw Markdown easier to copy.
- If wrapping the PR body in a code fence, prevent nesting breakage by escaping inner fences or using a longer outer fence such as four or more backticks.
- Keep the output stable even when the PR body contains nested code blocks.
- Prioritize practical use and copyability.
- Treat the PR body as ready-to-paste working text for GitHub, not as a generated sample.

Review method rules:

- Include review methods only when they help reviewers understand how to confirm the change.
- Do not use review methods merely to list build, lint, or test commands.
- Prefer explaining which review perspectives to check and how to check them.
- Include CI results or verified commands in detail only when the user explicitly asks.
- Omit the review method section when it is not useful.

Use these PR body headings in this order when relevant: `## このPull Requestの趣旨と目的`, `## 実装した内容・変更箇所`, `## レビューしてほしい箇所`, and optionally `## レビュー方法`. The review method heading is not required; add it only when useful.

Do not run `git push` or PR creation commands unless the user explicitly permits them.
