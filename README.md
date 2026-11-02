# Agentic workflow

## Before the workshop

Have these tools installed and working **before you arrive**. The workshop uses **GitHub Copilot CLI** in your terminal.

| Required tool | Check in your terminal | Official installation instructions |
| --- | --- | --- |
| Git | `git --version` | [Install Git](https://git-scm.com/install) |
| GitHub CLI (`gh`) | `gh --version` | [Install GitHub CLI](https://github.com/cli/cli#installation) |
| GitHub Copilot CLI (`copilot`) | `copilot --version` | [Install Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli) |
| OpenSpec | `openspec --version` | [Install OpenSpec](https://github.com/Fission-AI/OpenSpec/blob/main/docs/installation.md) |

Use a terminal and a web browser. On Windows, Copilot CLI requires PowerShell 6 or later.

### Install Copilot CLI

Choose the command for your system, or use the other options in the official instructions linked above.

macOS or Linux, with the standalone installer:

```sh
curl -fsSL https://gh.io/copilot-install | bash
```

Windows, with WinGet:

```powershell
winget install GitHub.Copilot
```

Node.js is **not** required for the standalone install. If you choose the npm installation method instead, Copilot CLI requires Node.js 22 or later.

### Install OpenSpec

macOS or Linux:

```sh
curl -fsSL https://raw.githubusercontent.com/Fission-AI/OpenSpec/main/scripts/binary/install.sh | sh
```

Windows, in PowerShell:

```powershell
irm https://raw.githubusercontent.com/Fission-AI/OpenSpec/main/scripts/binary/install.ps1 | iex
```

The standalone OpenSpec install does not require Node.js. If `openspec` is not found after installation, follow the official instructions to add its installation folder to your `PATH`, then open a new terminal and check again.

### Accounts and repository access

These are access requirements, separate from installing the tools:

- Have a GitHub account with an active Copilot subscription. If your organization manages it, ask your administrator to confirm that Copilot CLI access is enabled.
- Sign in to GitHub CLI with `gh auth login`. Check that `gh auth status` reports that you are logged in to `github.com`.
- Start `copilot`. If it asks you to sign in, enter `/login` and follow the prompts. Confirm that you can start a conversation before the workshop.
- Have write access to the team repository you will work in, and a local checkout of it. Know its GitHub name (`owner/repository`) and the full path to your checkout.

### Optional desktop app

You may also install the [GitHub Copilot Desktop App](https://github.com/features/copilot). It is optional and does not replace the required GitHub Copilot CLI installation.

### Workflow setup

The shared workflow files and setup instructions will be added here separately. This page is the preparation checklist, not an instruction to install the workflow yet.
