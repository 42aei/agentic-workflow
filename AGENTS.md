If asked to change this repository, not to set up a workflow, skip the setup below and follow the repository's contribution instructions.

# Set up the workflow

## How to behave during setup

- Explain each step before doing it, in plain words and in the developer's language.
- Ask one question at a time.
- Name each write outside the harness configuration and wait for a yes before it. To wait, end your turn with the question. Run no tool and take no further step after asking. The developer's next message is the answer and the session continues; you are not left without an answer. A yes is a developer message that answers that question. The request to set up the workflow, and answers to other questions (for example the team repository), are not a yes. Run the write only in a later turn, after the developer's message says yes. Steps that need no yes (checks and the role and skill installs inside the harness's own folders) may still run before the question, in the same turn.
- A question is the last sentence of your reply. Ask it even when the session looks non-interactive or you think nobody can answer: the developer's next message is the answer. Never write that you cannot ask or cannot get approval, and never go on without the yes.
- Never clone a repository during setup, the team repository included: no `git clone`, no `gh repo clone`. Use only a checkout the developer names.
- "A write outside the harness configuration" is any write outside the harness's own user-level folders (`~/.claude`, `~/.codex`, `~/.copilot`, `~/.agents`), for example a file in the team repository's project or the install of a missing tool. A change to an existing setting inside those folders (for example Codex `config.toml`) is asked for too.
- Never overwrite an existing target file silently: show the difference and ask before replacing it.
- Install nothing to run a check: no package installs or parser downloads. Leave no temporary files in the harness folders (`~/.claude`, `~/.codex`, `~/.copilot`, `~/.agents`). When no parser is available, check the file by eye; for frontmatter, the lines between the two `---` lines are `key: value` lines. The install of a missing git, gh or OpenSpec in "Install a missing tool" is not a check: it is an install the developer agreed to.

## Which harness

Ask which harness the developer uses. For Claude Code, follow the Claude Code section. For Codex CLI, follow the Codex CLI section. For Copilot CLI, follow the Copilot CLI section. Anything else is not covered yet; tell the developer and stop.

## Prerequisites

Run these checks in order. Explain each check in plain words before you run it, the repository permission check included (what it reads and why). Before the repository permission check, ask for the team repository as `owner/name`, then, as a separate question, for the absolute path of the developer's local checkout of it, or none. When git, gh or OpenSpec is missing, follow "Install a missing tool" below, then check again. On any other failure, explain what failed, give the fix below, and stop. Do not continue past a failed prerequisite.

| Check command | Pass | If it fails |
|---|---|---|
| `git --version` | Prints a version | Follow "Install a missing tool" for git. |
| `gh --version` | Prints a version | Follow "Install a missing tool" for gh. |
| `gh auth status` | Shows `Logged in to github.com` (an error for another host does not matter) | Tell the developer to run `gh auth login`. |
| `openspec --version` | Prints a version | Follow "Install a missing tool" for OpenSpec. |
| `gh repo view <team repo> --json viewerPermission` | `viewerPermission` is `WRITE`, `MAINTAIN` or `ADMIN` | Tell the developer to ask a repository admin for write access. |

### Check repository readiness

Use the existing checkout the developer named. If there is none, report that project readiness is unverified and continue with user-level role and skill installation only.

- Check `git -C "<checkout>" remote get-url origin`. It must identify the team repository; otherwise stop project setup and ask the developer to correct the checkout or repository name. Do not change the remote.
- Run `git -C "<checkout>" var GIT_AUTHOR_IDENT` and `git -C "<checkout>" var GIT_COMMITTER_IDENT`. If either fails, ask the developer to configure their own Git name and email, then stop. Do not invent an identity or change Git settings.
- Inspect `gh repo view <team repo> --json viewerPermission,isArchived,isFork,defaultBranchRef,mergeCommitAllowed,squashMergeAllowed,rebaseMergeAllowed` and the default branch's published rules with `gh api "repos/<team repo>/rules/branches/<default branch>"`. An archived repository or `squashMergeAllowed: false` blocks project setup: this workflow requires squash merging. Do not change repository settings or substitute another merge method. If rules cannot be read, report that merge requirements remain unverified; do not treat an API error as absence of rules.
- Explain that repository write permission does not prove that Git's push credentials work, that a branch may be pushed, or that a particular PR may be created or merged. Report these as unverified until the agreed functional PR provides evidence. Do not create a test commit, push, PR or access change just to probe permissions. Existing branch rules and required approvals still apply.

## Install a missing tool

Used when `git --version`, `gh --version` or `openspec --version` fails. Installing software is a write outside the harness folders, so it needs a yes. The commands and links below are from each tool's official documentation.

### git

- macOS and Linux: `brew install git` with Homebrew. On Debian and Ubuntu without Homebrew: `sudo apt install git`.
- Windows (PowerShell): `winget install --id Git.Git -e --source winget`
- Official instructions: https://git-scm.com/install

### gh

- macOS and Linux: `brew install gh` with Homebrew. Without Homebrew, see the link.
- Windows (PowerShell): `winget install --id GitHub.cli --source winget`
- Official instructions: https://github.com/cli/cli#installation

### OpenSpec

- macOS and Linux: `curl -fsSL https://raw.githubusercontent.com/Fission-AI/OpenSpec/main/scripts/binary/install.sh | sh`
- Windows (PowerShell): `irm https://raw.githubusercontent.com/Fission-AI/OpenSpec/main/scripts/binary/install.ps1 | iex`
- Official instructions: https://github.com/Fission-AI/OpenSpec/blob/main/docs/installation.md
- It needs no Node.js. The installer checks the download against the release's checksums and writes one file of 73-99 MB to `~/.local/bin` (Windows: `$env:LOCALAPPDATA\Programs\openspec`). On Alpine Linux, run `apk add libstdc++ libgcc` first.

### Steps

1. Detect the operating system with `uname -s`: `Darwin` is macOS and `Linux` is Linux. A `uname` that is not found, or output that contains `MINGW`, `MSYS` or `CYGWIN`, means Windows. Do not ask the developer when this answers it.
2. For OpenSpec only: if `openspec --version` failed, run the binary at the installer's folder: `~/.local/bin/openspec --version` (Windows PowerShell: `& "$env:LOCALAPPDATA\Programs\openspec\openspec.exe" --version`). If that works, OpenSpec is installed but off `PATH`: do not install it again. Tell the developer the folder is not on `PATH` and how to add it for their shell; do not edit shell startup files; use the full path for later calls, and go on with the next prerequisite.
3. Say in plain words which tool is missing. Show the command for that system and the official link. For OpenSpec, also say that it writes one 73-99 MB file to `~/.local/bin`. Ask for a yes to run the command. Then stop: your turn ends with that question.
4. Only when the developer's next message says yes, run the command. If the developer says no, install nothing and stop: setup needs the tool.
5. If the harness refuses the command, or the command needs sudo or administrator rights you do not have, do not look for another way to install the tool. Give copy-paste instructions instead: the command for the developer's system, the official link, and a request to tell you when it is done. Then stop. When the developer says it is done, check again.
6. Check again with the same command that failed. If `openspec --version` is still not found, try the installer's-folder check and follow the off-`PATH` guidance in step 2 first. If a check still fails on Windows after an install, tell the developer to open a new terminal and restart setup there: winget installs of git and gh need a new terminal to be found. Otherwise, if a check still fails, report its output and stop.

## Add OpenSpec to the project

The project is the developer's local checkout of the team repository, the path from Prerequisites. OpenSpec lives in the project so the whole team shares it; the roles and skills stay at user level. The first developer adds the OpenSpec files through a pull request; everyone else reuses them. The checkout is never created by setup (see "Never clone" above). Write the checkout path in double quotes in every command below, so a path with spaces and PowerShell both work.

1. No checkout: write nothing to any project. Say that OpenSpec was not added, and tell the developer to clone the team repository and run setup again from there. Go on with the harness section.
2. `git -C "<checkout>" remote get-url origin` must name the team repository; if it does not, treat it as no checkout (step 1).
3. Look without writing anything to the checkout (no `git fetch`, no checkout of a branch yet). Read the default branch with `git -C "<checkout>" ls-remote --symref origin HEAD` (the first line is `ref: refs/heads/<default branch>` followed by `HEAD`). Then run `gh api "repos/<team repo>/contents/openspec?ref=<default branch>" --silent`. If it succeeds, OpenSpec is already in the project: say so, write nothing, open no PR, and go on, whatever the checkout's state is. `gh: Not Found (HTTP 404)` means it is not there yet. Any other error: report it and stop.
4. Before you ask, check that nothing is in the way, read-only. `git -C "<checkout>" status --porcelain` must print nothing; if it prints something, write nothing, tell the developer to commit or stash and run setup again, give `git -C "<checkout>" stash push -u` with the real checkout path filled in, and say the developer gets the work back with `git -C "<checkout>" stash pop` after setup, and go on. `git -C "<checkout>" ls-remote --heads origin add-openspec` must print nothing; if it prints a line, the branch exists on the remote, so someone may already have opened this pull request: write nothing, tell the developer to look at the open pull requests of the team repository, give `gh pr list --repo <team repository> --head add-openspec` with the real team repository filled in, and go on.
   Before asking or running init, inspect the checkout's filesystem, including ignored files. If `openspec/`, `.claude/commands/opsx/`, or any `.claude/skills/openspec-*` or `.agents/skills/openspec-*` target already exists, write nothing and report the collision. Do not overwrite or include those existing files. Other skills in the parent folders are not part of this installation.
5. Name what will be added: the folder `openspec/`, the OpenSpec skills in `.claude/skills/` and `.agents/skills/` (Claude Code reads the first, Codex CLI the second, Copilot CLI both), and the commands in `.claude/commands/opsx/`. Say they go on a new branch `add-openspec` and into a pull request to the default branch, and that nothing is pushed to the default branch. Ask for a yes. Then stop: your turn ends with that question.
6. Only when the developer's next message says yes, do these in order:
   1. Remember where the checkout is: `git -C "<checkout>" rev-parse --abbrev-ref HEAD`. If it prints `HEAD` the checkout is detached: remember the commit from `git -C "<checkout>" rev-parse HEAD` instead.
   2. `git -C "<checkout>" fetch origin`, then `git -C "<checkout>" switch --no-track -c add-openspec origin/<default branch>`.
   3. `openspec init --tools "claude,codex" --no-copilot-cloud "<checkout>"`. The one command serves every harness, so a developer on another harness has nothing to add. `--no-copilot-cloud` keeps out the GitHub Actions workflow.
   4. `git -C "<checkout>" status --porcelain` must show only new paths (lines starting with `??`). If a tracked file is modified, do not commit: tell the developer and go to step 7. Ignored files are not listed, so this holds when the project's `.gitignore` ignores `.claude/`.
   5. Add only the generated targets checked absent before init: `git -C "<checkout>" add -f -- openspec ".agents/skills/openspec-*" ".claude/skills/openspec-*" .claude/commands/opsx`. Never stage the whole `.agents` or `.claude` directory. Git expands the quoted patterns itself, so this works in sh and PowerShell. Never change `.gitignore`. Inspect `git -C "<checkout>" diff --cached --name-status` and `git -C "<checkout>" diff --cached` before committing. Every staged file must be newly generated under those targets; if anything else is staged, stop without committing or pushing and report it. Then `git -C "<checkout>" commit -m "Add OpenSpec"`, then `git -C "<checkout>" push -u origin add-openspec`.
   6. `gh pr create --repo <team repo> --base <default branch> --head add-openspec --title "Add OpenSpec" --body "Adds the OpenSpec project files and skills from openspec init. Merge it so the team shares them."`
   7. Switch the checkout back: `git -C "<checkout>" switch <remembered branch>`, or for a detached checkout `git -C "<checkout>" switch --detach <remembered commit>`.
   8. Give the developer the pull request URL and the command to merge it, `gh pr merge <URL> --squash` with the real URL filled in, and say: others get the files when it is merged.
7. If the developer says no, add nothing and go on. If a command in step 6 fails, report its output, do not try another way, never merge and never push to the default branch, and put the checkout back as step 6.7 says. After a failure the new branch and the files from `openspec init` may remain in the checkout; leave them and tell the developer what is there.

## Claude Code

### Install the roles and skills

Install at the developer's user level, not in the team repository. For every existing target, show the difference and ask before replacing it.

For each of `roles/lead.md`, `roles/dev.md` and `roles/review.md`, write `~/.claude/agents/<role>.md`:
- Copy the header fields `name`, `description` and `skills`.
- Convert `read_only: true` to `tools: Read, Grep, Glob, Bash`.
- For `read_only: false`, add no `tools` line.
- Drop `read_only` itself. Keep the body unchanged.

Copy `skills/workflow/SKILL.md` to `~/.claude/skills/workflow/SKILL.md`, unchanged.
Copy `skills/show-me/SKILL.md` and `skills/show-me/LICENSE` to `~/.claude/skills/show-me/`, unchanged. Copy `LICENSE` because the MIT terms require it to travel with the skill.
Copy `skills/doc-coauthoring/SKILL.md` to `~/.claude/skills/doc-coauthoring/SKILL.md`, unchanged.

Then follow `Add OpenSpec to the project` above.

### Verify and hand over

- List the three installed agent files and the four installed skill files (workflow/SKILL.md, show-me/SKILL.md, show-me/LICENSE, doc-coauthoring/SKILL.md) with their full paths.
- Check that each agent file's frontmatter parses, has `name` and `description`, and that review has `tools: Read, Grep, Glob, Bash`.
- Tell the developer the pull request URL from "Add OpenSpec to the project", or why nothing was added.
- Tell the developer: open a session in the team repository and type `start lead on <request>`.

## Codex CLI

This section was written and tested with codex-cli 0.165.0.

### Install the roles and skills

Install at the developer's user level, not in the team repository. For every existing target, show the difference and ask before replacing it.

For each of `roles/lead.md`, `roles/dev.md` and `roles/review.md`, write `~/.codex/agents/<role>.toml` (create the folder if missing) with this shape:

```toml
name = "<name>"
description = "<description>"
developer_instructions = '''
<top sentences>

<body>
'''
```

- Copy `name` and `description` from the header. Write `description` as a TOML basic string on one line; escape `\` as `\\` and `"` as `\"`.
- Use everything after the header's closing `---` as `<body>`, unchanged and without the blank line right after the header. Put it in the TOML multi-line literal string `'''...'''`; backslashes and quotes need no escaping. If a body contains `'''`, stop and tell the developer; do not change the body.
- Put `<top sentences>` first, each on its own line, in this order: for `read_only: true`, add `You are read-only: do not edit files, commit, push or merge; use shell commands only to run tests and read.` Then for `skills`, add `Use the skills workflow.` using the header's skill names joined with `, `. Add no skills sentence when the header has no `skills`.
- Drop `read_only` and `skills` after converting. `read_only: false` adds no sentence. Codex has no tool restriction for custom agents, so review is read-only by these instructions only.

Copy `skills/workflow/SKILL.md` to `~/.agents/skills/workflow/SKILL.md`, unchanged. Codex reads user skills from `~/.agents/skills`, not `~/.codex`.
Copy `skills/show-me/SKILL.md` and `skills/show-me/LICENSE` to `~/.agents/skills/show-me/`, unchanged. Copy `LICENSE` because the MIT terms require it to travel with the skill.
Copy `skills/doc-coauthoring/SKILL.md` to `~/.agents/skills/doc-coauthoring/SKILL.md`, unchanged.

### Configure `~/.codex/config.toml`

Read `~/.codex/config.toml` first; it may not exist. Keep every existing key and table exactly as it is. Never rewrite the file; only add or edit the lines named here. Ask for each of these two changes separately: name the write, explain why, and wait for a yes.

(a) Nested agents. Lead starts dev and review as subagents, which makes them depth 2. Codex's default `max_depth = 1` blocks that, so lead cannot start them. Set `max_depth = 2` under `[agents]`. If the table is missing, append `[agents]` and `max_depth = 2` at the end of the file; a new table there moves no existing key. If `[agents]` exists, add or edit only its `max_depth` line. If it is already 2 or more, say so and change nothing. If the developer says no, tell them lead cannot start dev and review, and go on.

(b) Worktree writes for lead and dev. Lead creates `.worktrees/<change>` inside the team repository checkout; dev continues there. Creating and committing in it writes under the checkout's `.git`. Codex's default sandbox (`workspace-write`) makes `.git` read-only, so `git worktree add` fails with `Read-only file system`. Use the checkout path from Prerequisites. Add this minimal grant:

```toml
[sandbox_workspace_write]
writable_roots = ["<checkout>/.git"]
```

If `[sandbox_workspace_write]` or `writable_roots` already exists, add the path to the existing list and keep its entries. Skip (b) when the developer has no local checkout yet. If the developer says no, tell them lead stops at its first `git worktree add` until the line exists. The grant lets every Codex session in that checkout write to `.git`.

Then follow `Add OpenSpec to the project` above.

### Verify and hand over

- List the three installed agent files and the four installed skill files (workflow/SKILL.md, show-me/SKILL.md, show-me/LICENSE, doc-coauthoring/SKILL.md) with their full paths.
- Parse each agent file and `~/.codex/config.toml` as TOML: `python3 -c 'import tomllib,sys; [tomllib.load(open(f,"rb")) for f in sys.argv[1:]]' <files>` (when `python3` is older than 3.11, check by eye instead that each agent file has `name`, `description` and `developer_instructions`). Check that each agent has `name`, `description` and `developer_instructions`, that review's `developer_instructions` starts with `You are read-only`, and that `config.toml` has `max_depth = 2` under `[agents]` when the developer agreed.
- Tell the developer the pull request URL from "Add OpenSpec to the project", or why nothing was added.
- Tell the developer: open `codex` in the team repository and type `start lead on <request>`.

## Copilot CLI

This section was written and tested with Copilot CLI 1.0.105.

### Install the roles and skills

Install at the developer's user level, not in the team repository. For every existing target, show the difference and ask before replacing it.

For each of `roles/lead.md`, `roles/dev.md` and `roles/review.md`, write `~/.copilot/agents/<role>.agent.md` (create the folder if missing) with this shape:

```markdown
---
name: <name>
description: <description>
tools: [read, search, execute]
---

Use the skills workflow.

<body>
```

- Copy the header lines `name` and `description` unchanged; they are already valid YAML.
- For `read_only: true`, add the `tools: [read, search, execute]` line. These are Copilot's names for the read, search (grep and glob) and shell tools; the list has no edit tool, so Copilot's file-editing tools are off. Shell stays on, so review can still write through it; keeping that from happening is up to its instructions. For `read_only: false`, add no `tools` line (all tools).
- Copilot agent files have no `skills` field. When the header has `skills`, put `Use the skills workflow.` as the first line of the body (the header's skill names joined with `, `), followed by a blank line. Add no such line when the header has no `skills`.
- Drop `read_only` and `skills` from the header. Use everything after the header's closing `---` as `<body>`, unchanged and without the blank line right after the header.

Copilot CLI reads user skills from `~/.agents/skills`, the same folder the Codex CLI section installs to, so one copy serves both harnesses. Copy `skills/workflow/SKILL.md` to `~/.agents/skills/workflow/SKILL.md`, unchanged. Copy `skills/show-me/SKILL.md` and `skills/show-me/LICENSE` to `~/.agents/skills/show-me/`, unchanged. Copy `LICENSE` because the MIT terms require it to travel with the skill. Copy `skills/doc-coauthoring/SKILL.md` to `~/.agents/skills/doc-coauthoring/SKILL.md`, unchanged. A target that already exists with identical content needs no write: say so and go on. Do not use `~/.copilot/skills`.

Lead and dev work in `.worktrees/<change>` inside the team repository checkout, so this setup writes no Copilot configuration for it. Tell the developer to confirm the folder as trusted when `copilot` first asks in the checkout, and approve lead's and dev's shell and file-write requests when asked: a worktree outside the checkout is refused even with shell and write approved. Never use `--allow-all`.

Then follow `Add OpenSpec to the project` above.

### Verify and hand over

- List the three installed agent files and the four installed skill files (workflow/SKILL.md, show-me/SKILL.md, show-me/LICENSE, doc-coauthoring/SKILL.md) with their full paths.
- Check that each agent file's frontmatter parses as YAML and has `name` and `description`, that review's `tools` is `[read, search, execute]`, and that lead and dev have no `tools` line. Example: `python3 -c 'import sys,yaml; [yaml.safe_load(open(f).read().split("---")[1]) for f in sys.argv[1:]]' <files>` (needs PyYAML; otherwise check by eye that the lines between the two `---` are `key: value` lines).
- Tell the developer the pull request URL from "Add OpenSpec to the project", or why nothing was added.
- Tell the developer: open `copilot` in the team repository and type `start lead on <request>`.
