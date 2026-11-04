# Agentic workflow

## Before the workshop

Install **GitHub Copilot CLI** and sign in. You need a GitHub account with Copilot access. 

macOS / Linux:

```sh
curl -fsSL https://gh.io/copilot-install | bash
```

Windows, in PowerShell 6 or later:

```powershell
winget install GitHub.Copilot
```

Run `copilot --version`, then start `copilot`. Use `/login` if prompted and check that you can send a message and receive a reply.

[Other installation options](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli). For Copilot CLI, Node.js is needed only for the npm option (version 22+).

### Get an agent to perform the reset of the setup.

Git, GitHub CLI, OpenSpec and agent-browser with its browser installed are required. GitHub CLI and Copilot CLI must be signed in. agent-browser lets the workflow verify browser-visible behaviour in the running app. Paste this into Copilot CLI for help:

```text
Help me prepare for the workshop. Read the Prerequisites and Install a
missing tool sections of SETUP.md at https://github.com/42aei/agentic-workflow.
Check git --version, gh --version, openspec --version and
agent-browser --version. For each missing tool, show the official installation
instructions and command for my system, then ask before installing it.
Follow SETUP.md's Check the browser section too; ask before downloading
the browser or installing system libraries. Do not install packages just
to run a check. Check gh auth status. If login is needed, guide me through gh auth login;
I will complete login and consent myself. Never ask me to paste a token.
Ask before changing settings. If I decline a required step or a command
fails, stop and explain what I need to do. Do not clone a repository,
initialize OpenSpec in a project or install workflow files yet.
```

This prepares the tools and GitHub login, not the workflow. To install the tools yourself, use the official instructions:

| Tool | Check | Install manually |
| --- | --- | --- |
| Git | `git --version` | [Git](https://git-scm.com/install) |
| GitHub CLI | `gh --version` | [GitHub CLI](https://github.com/cli/cli#installation) |
| OpenSpec | `openspec --version` | [OpenSpec](https://github.com/Fission-AI/OpenSpec/blob/main/docs/installation.md) |
| agent-browser and browser | `agent-browser --version`, then [Check the browser](SETUP.md#check-the-browser) | [agent-browser](https://github.com/vercel-labs/agent-browser#install); install the CLI, then run `agent-browser install` (`agent-browser install --with-deps` on Linux). |

The agent-browser npm option needs Node.js and npm; Homebrew is an alternative on macOS and Linux. See [setup](SETUP.md#agent-browser) for platform-specific commands and approval steps.

After installing GitHub CLI, run `gh auth login`. This login is separate from Copilot's.

Have a local checkout of your team's repository and permission to push, create PRs and merge the workshop changes. Know the repository name (`owner/repository`) and checkout path. Configure your Git name and email. Setup will not clone the repository for you.

## Optional

[GitHub Copilot Desktop App](https://github.com/features/copilot). The workflow is not yet verified in Desktop. Use CLI for the workshop.

## Set up the workflow

Paste this into Copilot CLI:

```text
Read SETUP.md in https://github.com/42aei/agentic-workflow and set up the workflow for me.
```
