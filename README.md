# WSL Dev Environment Setup

This guide sets up a WSL-based development environment on Windows, including Ubuntu, VS Code Remote WSL, OpenCode, Node.js via `nvm`, and optional project skills.

## 1. Install WSL and Ubuntu

Run this in PowerShell:

```powershell
wsl --install -d Ubuntu
```

Restart Windows if prompted. After the restart, open Ubuntu from the Start menu and complete the Linux user setup.

To verify the installation, run this in PowerShell:

```powershell
wsl --status
wsl --list --verbose
```

## 2. Configure Windows Terminal

After installation, Windows Terminal usually creates an `Ubuntu` profile automatically.

If you prefer a custom profile:

1. Open Windows Terminal settings.
2. Duplicate or create a profile named `Dev`.
3. Set the command line to:

```text
wsl.exe -d Ubuntu
```

Optionally hide or delete the generated `Ubuntu` profile if you only want to use the custom `Dev` profile.

## 3. Create a Projects Folder

Run this inside Ubuntu/WSL:

```bash
mkdir -p ~/projects
```

To add a shortcut, open your shell config:

```bash
nano ~/.bashrc
```

Add this at the bottom:

```bash
# Custom
alias proj='cd ~/projects'
```

Reload the shell config:

```bash
source ~/.bashrc
```

You can now jump to your projects folder with:

```bash
proj
```

## 4. Resolve VS Code Remote WSL Issues

Install the VS Code extension named `WSL` by Microsoft.

From inside WSL, the normal way to open a project is:

```bash
cd ~/projects/<project-path>
code .
```

If VS Code opens the folder locally on Windows instead of remotely in WSL, open it once with an explicit remote URI:

```bash
code --folder-uri "vscode-remote://wsl+Ubuntu/home/<username>/projects/<project-path>"
```

Replace `<username>` and `<project-path>` with your actual Linux username and project path.

## 5. Install OpenCode

Run this inside WSL:

```bash
curl -fsSL https://opencode.ai/install | bash
```

Restart your terminal, or reload your shell config if the installer updated it:

```bash
source ~/.bashrc
```

Optional alias:

```bash
alias oc='opencode'
```

To make the alias permanent, add it to the bottom of `~/.bashrc`.

## 6. Connect OpenCode to a Provider

Prefer subscription-based authentication over API keys where possible.

Start OpenCode:

```bash
opencode
```

Then run this inside OpenCode:

```text
/connect
```

## 7. Install Node.js with nvm

If you already have Node.js or npm installed on Windows, install them again inside WSL. WSL has its own Linux environment and should not rely on the Windows installation.

Run this inside WSL:

```bash
sudo apt update
sudo apt install -y curl

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source ~/.bashrc

nvm install --lts
nvm use --lts
node --version
npm --version
```

## 8. Set Up Project AI Context

For each project, it is useful to add three kinds of context:

1. Project-specific skills for repeatable workflows.
2. An `AGENTS.md` file for agent instructions.
3. An optional `DESIGN.md` file for product, UI, and architecture decisions.

### Install Project Skills

Skills are project-level helpers that teach AI tools specific workflows, conventions, or review patterns.

Install skills from inside the project where you want them to be available:

```bash
cd ~/projects/<project-path>
```

Example for Go projects:

```bash
npx skills add https://github.com/samber/cc-skills-golang --skill golang-error-handling
```

After installing a skill, check whether it created or changed project files. Commit those files with the project if the skill should be shared with other contributors.

Useful skill notes:

1. Install only skills that match the project stack or workflow.
2. Prefer project-local skills over global assumptions, so every project can define its own rules.
3. Re-run or update skills when project conventions change.
4. Keep skills focused. A small skill for one workflow is easier to trust than a large generic one.

### Add `AGENTS.md`

Add an `AGENTS.md` file to the project root. This file tells coding agents how to work in the repository.

Example:

```markdown
# Agent Instructions

## Project Overview

Briefly describe what this project does and who it is for.

## Development Commands

- Install dependencies: `npm install`
- Start dev server: `npm run dev`
- Run tests: `npm test`
- Run linting: `npm run lint`
- Build: `npm run build`

## Coding Conventions

- Follow the existing code style.
- Prefer small, focused changes.
- Do not add new dependencies unless clearly needed.
- Keep public APIs stable unless the task explicitly requires a breaking change.

## Testing Expectations

- Run relevant tests after changing behavior.
- Add or update tests for bug fixes and new features.
- If tests cannot be run, explain why in the final response.

## Design Context

- If this project contains a `DESIGN.md` file, read it before making UI, UX, copywriting, product-flow, or architecture decisions.
- Follow the design direction, UX rules, and recorded decisions in `DESIGN.md`.
- If a requested change conflicts with `DESIGN.md`, mention the conflict before changing the design direction.

## Repository Notes

- Document important folders, generated files, or setup requirements here.
- Mention files or directories agents should avoid editing directly.
```

Keep `AGENTS.md` factual and practical. Avoid vague preferences such as "write clean code" unless they are backed by concrete project rules.

### Add Optional `DESIGN.md`

For UI-heavy projects, products, or apps with important user flows, add a `DESIGN.md` file to the project root.

Use it to capture product and design context that should survive beyond a single chat session.

Example:

```markdown
# Design Notes

## Product Goal

Describe the main user problem this project solves.

## Target Users

Describe the primary users and their expectations.

## Visual Direction

- Overall style:
- Typography:
- Color palette:
- Spacing and layout:
- Motion/interaction style:

## Key Flows

- Onboarding:
- Main workflow:
- Error and empty states:

## UX Rules

- Prefer clear labels over clever wording.
- Make destructive actions explicit and reversible where possible.
- Keep mobile and desktop layouts usable.

## Decisions

- Record important design decisions here with dates and short reasoning.
```

Use `DESIGN.md` when design consistency matters. Skip it for small scripts, libraries, or backend-only projects unless there are important product decisions to preserve.
