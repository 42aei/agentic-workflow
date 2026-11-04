# Set up the workflow

Prepare the workshop prerequisites, then install this repository's workflow agents and skills for the developer's Copilot CLI user. No project folder or team repository is needed. Do not ask for one, clone a repository, initialize OpenSpec in a project, or create commits or pull requests. Do not start the workflow during setup.

## How to guide setup

- Explain each step in plain words and in the developer's language. Ask one question at a time.
- Check what is already installed before proposing an install. Never install packages or parsers just to run a check.
- Before installing software or changing settings, show the command, explain what it changes and ask for approval. End the turn and wait for the developer's answer before running it. The request to run setup is not approval for an install.
- If approval is declined, a command is refused or a check still fails after the approved fix, report what is incomplete and stop. Do not try another installation route or change settings to work around it.
- The developer completes interactive login and consent themselves. Never ask them to paste tokens or print secret values.

## Prerequisites

Explain each check before running it, in this order. Skip installs for tools that already work. For a missing tool, follow its installation instructions below, then repeat the failed check before continuing.

| Check | Pass | If it fails |
|---|---|---|
| `git --version` | Prints a version | Offer the Git install below. |
| `gh --version` | Prints a version | Offer the GitHub CLI install below. |
| `gh auth status` | Shows `Logged in to github.com` | Guide the developer through `gh auth login`, then check again. An error for another host does not matter. |
| `openspec --version` | Prints a version | Check the fallback path below before offering an install. |
| `agent-browser --version` | Prints a version | Offer the agent-browser CLI install below. |
| [Browser smoke check](#check-the-browser) | Opens a blank page, reads its title and snapshot, and closes the session | Follow the missing-browser guidance below. A CLI version alone is not a pass. |

## Install a missing tool

Detect the operating system before choosing a command. `uname -s` reports `Darwin` for macOS and `Linux` for Linux; `MINGW`, `MSYS` or `CYGWIN` indicates Windows. If the system is still unclear, ask. Do not install a package manager just to use these instructions.

Show the relevant command and official link, explain what it installs, and wait for approval. If it needs administrator rights or interactive input the agent cannot provide, give the developer that one command to run and wait for them to finish. Then repeat the check. On Windows, a new terminal may be needed after installation.

### Git

- With Homebrew on macOS or Linux: `brew install git`.
- Debian or Ubuntu without Homebrew: `sudo apt install git`.
- Windows PowerShell: `winget install --id Git.Git -e --source winget`.
- Other systems: use the [official Git instructions](https://git-scm.com/install).

### GitHub CLI

- With Homebrew on macOS or Linux: `brew install gh`.
- Windows PowerShell: `winget install --id GitHub.cli --source winget`.
- Other systems: use the [official GitHub CLI instructions](https://github.com/cli/cli#installation).
- After installation, guide the developer through `gh auth login` and check `gh auth status` again. This login is separate from the coding assistant's login.

### OpenSpec

First check the default install path if `openspec` is not found:

- macOS or Linux: `~/.local/bin/openspec --version`.
- Windows PowerShell: `& "$env:LOCALAPPDATA\Programs\openspec\openspec.exe" --version`.

If that works, do not reinstall. Explain how to add its folder to `PATH` for the developer's shell, without editing shell startup files, and use the full path for the remaining check.

Otherwise, offer the [official OpenSpec installer](https://github.com/Fission-AI/OpenSpec/blob/main/docs/installation.md):

- macOS or Linux: `curl -fsSL https://raw.githubusercontent.com/Fission-AI/OpenSpec/main/scripts/binary/install.sh | sh`.
- Windows PowerShell: `irm https://raw.githubusercontent.com/Fission-AI/OpenSpec/main/scripts/binary/install.ps1 | iex`.

It needs no Node.js. The installer checks release checksums and writes one 73–99 MB binary to `~/.local/bin` (Windows: `$env:LOCALAPPDATA\Programs\openspec`). On Alpine Linux, it also needs `libstdc++` and `libgcc`; ask before installing them with `apk add libstdc++ libgcc`. Do not run `openspec init`: project setup is not part of this guide.

### agent-browser

Use the [official agent-browser instructions](https://github.com/vercel-labs/agent-browser#install):

- CLI with Homebrew on macOS or Linux: `brew install agent-browser`.
- CLI on Windows, or without Homebrew: `npm install -g agent-browser`. Check `node --version` and `npm --version` first. If either is missing, explain the dependency and guide the developer to [Node.js LTS with npm](https://nodejs.org/en/download), then stop until it is installed. Do not silently add a toolchain.
- Browser on macOS or Windows: `agent-browser install`.
- Browser on Linux: `agent-browser install --with-deps`. This also installs system libraries and may need administrator rights.

The browser download is Chrome for Testing in a regenerable tool cache, not project source or the developer's personal browser. Browser verification needs this runtime; workflow files cannot replace it. Ask separately before installing the CLI and before downloading the browser or installing system libraries. If the CLI works, do not reinstall it. Never use `npx` to download a tool implicitly.

## Check the browser

1. Run `agent-browser skills get core` and read the CLI's built-in, version-matched guide. Do not install a separate skill. If the command fails, report it and stop.
2. Before any browser/session command, inspect only the names of set `AGENT_BROWSER_*` environment variables and whether `~/.agent-browser/config.json` or `agent-browser.json` in the working directory exists. Do not print values or read config contents. Ignore only `AGENT_BROWSER_DEFAULT_TIMEOUT` and `AGENT_BROWSER_IDLE_TIMEOUT_MS`. If any other variable is set or either file exists, report only its name/path and ask for a clean environment and working directory, then stop. Do not unset variables, edit files or override settings. If this inspection is unavailable, report isolation as unverified and stop. Keep the checked environment and directory for all commands below.
3. Explain that the check opens only a blank page in a separate headless browser, creates transient session/cache files and does not use the developer's desktop browser or login state. Choose an unused session name using `agent-browser session list`; use that same name for every command:

   ```sh
   agent-browser --session workflow-setup-check open about:blank
   agent-browser --session workflow-setup-check get title
   agent-browser --session workflow-setup-check snapshot
   agent-browser --session workflow-setup-check close
   ```

   Each command must succeed. An empty title and a snapshot with no elements are expected. Once opened, close the session even if a later check fails. Never use `close --all`, attach to a running browser, load a personal profile or save authentication state.
4. If opening fails because the browser or system libraries are missing, explain the relevant install above and ask for approval. After installation, repeat the smoke check. For any other failure, report the failed command and output and stop. Do not switch browser tools, change browser settings or try privileged flags.

## Install the workflow for Copilot CLI

After all prerequisite checks pass, install only the files below at user level. `~` means the developer's home directory, including on Windows. These are Copilot's [personal agents](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli) and [personal skills](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-skills); no project files or other harness configuration are needed.

### Read the source and ask

Read the complete source files from this repository before writing anything. Use an existing checkout of `42aei/agentic-workflow` if available, otherwise read the files through GitHub without cloning. Use the same revision for all seven files; do not substitute the agent's own installed workflow or similarly named skills. If any source cannot be read, stop without installing an incomplete workflow.

| Source in this repository | Destination |
|---|---|
| `roles/lead.md` | `~/.copilot/agents/lead.agent.md` |
| `roles/dev.md` | `~/.copilot/agents/dev.agent.md` |
| `roles/review.md` | `~/.copilot/agents/review.agent.md` |
| `skills/workflow/SKILL.md` | `~/.agents/skills/workflow/SKILL.md` |
| `skills/show-me/SKILL.md` | `~/.agents/skills/show-me/SKILL.md` |
| `skills/show-me/LICENSE` | `~/.agents/skills/show-me/LICENSE` |
| `skills/doc-coauthoring/SKILL.md` | `~/.agents/skills/doc-coauthoring/SKILL.md` |

Inspect existing destinations first. Identical files need no write. Show the differences for any file that would be replaced. Explain the files and folders to be created or updated, then ask for approval and wait before writing. Never replace an existing file silently or change unrelated configuration.

### Write the agents and skills

For each role, convert its source frontmatter to Copilot's format:

- Copy `name` and `description` unchanged.
- For `read_only: true`, add `tools: [read, search, execute]`. These allow reading, searching and shell commands, not the file-editing tool. Shell can still write; the review role's instructions forbid that. For `read_only: false`, add no `tools` line.
- Drop `read_only` and `skills` from the frontmatter. When `skills` is present, add `Use the skills workflow.` as the first body line, using the source skill names joined with `, `, followed by a blank line.
- Keep the original body unchanged after that line, excluding the blank line immediately after the source frontmatter.

Copy the four skill files unchanged to their destinations in the table. Keep `show-me/LICENSE`: its MIT terms require it to travel with the skill. Create only the missing destination folders needed for these files. Do not install temporary files or download parsers into the configuration folders.

### Verify the files

Read back all seven installed files and compare them with the expected content. Verify that each agent has valid YAML frontmatter with `name` and `description`, that review has `tools: [read, search, execute]`, and that lead and dev have no `tools` line. Check that all three include `Use the skills workflow.` and preserve their source bodies. Compare the four skill files byte-for-byte with the source. Use an existing parser if available; otherwise inspect the simple frontmatter by eye. Do not install a parser to run this check.

## Finish

Report the prerequisite results, the full paths of the seven installed files and anything incomplete. Claim browser readiness only after a successful smoke check. Ask the developer to restart Copilot CLI, then use `/agent` to confirm that lead, dev and review appear; file checks alone do not prove that Copilot loaded them.

Stop here. The workflow is installed at user level, not configured for a project. Repository access, Git identity and OpenSpec project initialization remain later project setup. Do not tell the developer the workflow can run in an unprepared project.
