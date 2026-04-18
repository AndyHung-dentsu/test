---
name: git-auto-pr
description: Automates GitHub Pull Request creation with branch validation, PR template generation. IMPORTANT - Run /make-pr proactively when completing tasks, implementing features, or before merging code. Triggers include user requests like "create a PR", "make a pull request", "open a PR", or when work is ready for review. Always respond in Traditional Chinese (繁體中文).
---

# Git auto PR skill

Automates PR creation following team workflow conventions with branch validation, target branch prompts, PR description generation.

## Workflow

Execute these 6 steps sequentially:

### Step 1: Validate Branch

Run `git branch --show-current` and verify format: `master`, `feature/xxx`, `hotfix/xxx`, `spec/xxx`

- Valid: `master`, `feature/add-function`, `hotfix/fix-bug`, `spec/update-docs`
- Invalid: `main`, `task/123`

If invalid, output error and EXIT:

```
❌ 分支名稱不符合規則
目前分支：{current_branch}
要求格式：`master`, `feature/xxx`, `hotfix/xxx`, `spec/xxx`
```

### Step 2: Check GitHub CLI

Run `gh --version`. If not installed, output error and EXIT:

```
❌ 未安裝 GitHub CLI
安裝方式：https://cli.github.com/
```

NEVER auto-install unless explicitly requested.

### Step 3: Prompt for Target Branch

Use AskUserQuestion with options: "master", "其他分支"

Handle responses:

- **develop**: Use `master`
- **Other**: Prompt for branch name, validate existence with `git ls-remote --heads origin {branch_name}`. If invalid, show error and EXIT:

```
❌ 分支不存在
請確認分支名稱是否正確，並且已經推送到遠端。
```

### Step 4: Prompt for PR Title

Use AskUserQuestion with options: "PR 標題"

Handle responses:

- **PR 標題**: Use user input

### Step 5: Generate PR Description

**5.1 Gather info:**

```bash
git diff --stat {target_branch}...HEAD
git diff --name-status {target_branch}...HEAD
git log {target_branch}..HEAD --oneline
```

**5.2 Read template:**：Read content from `.claude/skills/git-auto-pr/references/pr-template.md`

**5.3 Fill template:**

- 背景描述: Why this PR is needed
- 實作方法: Implementation approach summary
- 實際變更: Checkbox list of main changes
- 測試驗證: Test scenarios as checkboxes
- 相關 PR：use `git log <target_branch>..<current_branch> --format="%H" | xargs -I {} gh pr list --search "{}" --state merged --json number,title,url --template '{{range .}}- [{{.title}}]({{.url}}) (#{{.number}}){{"\n"}}{{end}}' | sort -u` to find related PRs.

Use plain backticks (not `\``), as description passes through heredoc.

**5.4 Show draft:** Display full description, wait for user confirmation.

### Step 6: Push and Create PR

1. Check status: `git status -sb`
2. Push if needed: `git push -u origin {branch_name}`
3. Get username: `gh api user --jq .login`
4. Create PR:

```bash
gh pr create \
  --base {target_branch} \
  --title "{pr_title}" \
  --body "$(cat <<'EOF'
{pr_description}
EOF
)" \
  --assignee @me
```

6. Display confirmation with PR URL, title, target branch, assignee.

## Critical Rules

- NEVER skip Step 1 validation
- NEVER auto-install tools
- Always use heredoc for PR descriptions
- Show description before creating PR
