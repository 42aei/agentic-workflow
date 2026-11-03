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

[Other installation options](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli). Node.js is needed only for the npm option (version 22+).

Sign in and start copilot cli to run the next step (if using the agent path) 

### Get agent help or install manually (Ensure you are signed in before continuing) 

Git, GitHub CLI and OpenSpec are required. Paste this into Copilot CLI for help:

```text
Help me prepare for the workshop. Check git --version, gh --version and
openspec --version. For each missing tool, show the official installation
instructions and command for my system, then ask before installing it.
Check gh auth status. If login is needed, guide me through gh auth login;
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

After installing GitHub CLI, run `gh auth login`. This login is separate from Copilot's.

Have a local checkout of your team's repository and permission to push, create PRs and merge the workshop changes. Know the repository name (`owner/repository`) and checkout path. Configure your Git name and email. Setup will not clone the repository for you.

## Optional

[GitHub Copilot Desktop App](https://github.com/features/copilot). The workflow is not yet verified in Desktop. Use CLI for the workshop.

## Set up the workflow

Paste this into Copilot CLI:

```text
Read SETUP.md in https://github.com/42aei/agentic-workflow and set up the workflow for me.
```
