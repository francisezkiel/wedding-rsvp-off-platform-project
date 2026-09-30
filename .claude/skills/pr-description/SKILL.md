---
name: pr-description
description: Write a pull request title and description from the current branch's changes, then open the PR on GitHub with `gh pr create`. Use this whenever the user wants to open, create, submit, or raise a pull request / PR, asks for a PR description or PR summary, or says something like "ship this branch" or "put this up for review" — even if they don't say "description".
---

# PR description

Write a PR from what the branch actually changed, then open it. Reviewers rely on the description to know what to check, so accuracy matters more than polish: describe only what the diff contains.

## 1. Gather context

Run these from the repo root (the base branch is usually `main`; check `git remote show origin` if unsure):

```bash
git status --short
git log main..HEAD --oneline
git diff main...HEAD --stat
git diff main...HEAD
```

- If there are uncommitted changes, tell the user they won't be in the PR and ask whether to commit them first. Opening a PR that silently leaves out work is worse than a short pause.
- If the branch has no commits ahead of `main`, stop and say there's nothing to open a PR for.
- If `gh pr view` shows a PR already exists for this branch, offer to update its description (`gh pr edit --body`) instead of creating a duplicate.
- Note any issue number mentioned in the branch name or commit messages.

## 2. Write the title

- Under 70 characters, imperative mood: "Fix…", "Add…", "Update…".
- Say what changed for the reader, not which files were touched. "Fix duplicate step numbers in README" beats "Update README.md".

## 3. Write the description

Use this template:

```markdown
## Testing
- <how the change was checked>

Closes #<issue>   ← only if an issue number is known
```

Guidance for **Testing**:
- List what was genuinely done: tests run, pages previewed, manual steps. Look at the session history and the diff for evidence.
- If nothing was run, say so honestly and describe how a reviewer can check it (e.g. "Docs-only change; review the rendered README on the PR's Files tab"). Never claim tests that weren't run — a false "all tests pass" costs reviewers more than an honest "not tested".
- For UI or visual changes, suggest adding before/after screenshots.

## 4. Open the PR

Push the branch if it has no upstream, then create the PR. Pass the body through a heredoc so Markdown survives shell quoting:

```bash
git push -u origin HEAD
gh pr create --base main --title "<title>" --body "$(cat <<'EOF'
<description>
EOF
)"
```

Append any PR attribution footer the environment asks for to the end of the body.

Then share the PR URL that `gh` prints, along with the title and description you used, so the user can see what went up.
