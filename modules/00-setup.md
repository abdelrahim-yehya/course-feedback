# Module 0 — Setup & Orientation

**⏱ 15 minutes** · [← Back to README](../README.md) · [Next: Module 1 →](01-constitution-and-spec.md)

---

## Goal

Everyone has Spec Kit installed, a project initialized, an agent connected, and knows what the scaffold contains.

---

## 0.1 — Install the Prerequisites

If you completed the pre-work, skip to **0.2**.

| Tool | macOS | Linux | Windows (PowerShell) |
| --- | --- | --- | --- |
| **uv** | `brew install uv`<br>*or*<br>`curl -LsSf https://astral.sh/uv/install.sh \| sh` | `curl -LsSf https://astral.sh/uv/install.sh \| sh` | `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 \| iex"` |
| **Node.js** *(v20+)* | `brew install node`<br>*or* download from [nodejs.org](https://nodejs.org/) | Use `nvm` or download from [nodejs.org](https://nodejs.org/) | `winget install OpenJS.NodeJS.LTS`<br>*or* download from [nodejs.org](https://nodejs.org/) |
| **Git** | `brew install git`<br>*or* run `git --version` to prompt install | `sudo apt update && sudo apt install git -y` | `winget install --id Git.Git -e --source winget`<br>*or* download from [git-scm.com](https://git-scm.com/) |

---

### Verification

Restart your terminal window, then run:

```bash
node --version    # expect v20+
git --version     # expect git version 2.x+
uv --version      # expect uv 0.x+

---

## 0.2 — Install the Spec Kit CLI

```bash
uv tool install specify-cli
specify --version
```

<details>
<summary>Alternatives (pinned version, one-off run, no global install)</summary>

```bash
# Pin to a specific release — recommended when you want the whole room on identical output
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@v0.12.17

# One-off, no install
uvx --from git+https://github.com/github/spec-kit.git specify init

# pipx
pipx install specify-cli
```
</details>

---

## 0.3 — Initialize the project

```bash
mkdir course-feedback && cd course-feedback
specify init . --integration claude
```

Swap `claude` for your agent: `copilot`, `gemini`, `cursor`, `codex`, `windsurf`, etc. Omit `--integration` entirely and you get an interactive picker.

Then add the git extension — git operations are **not** installed by default any more, and we want feature branches and commits during this workshop:

```bash
specify extension add git
```

Verify your agent is reachable:

```bash
specify check
```

---

## 0.4 — Tour the scaffold

Open the directory. You should see something close to:

```
course-feedback/
├── .specify/
│   ├── memory/
│   │   └── constitution.md      ← project principles (empty template for now)
│   ├── templates/               ← the templates each command fills in
│   ├── scripts/
│   ├── extensions/
│   └── feature.json             ← which feature is currently active
├── .claude/  (or .github/, .cursor/ …)
│   └── commands/                ← the /speckit.* prompts your agent will run
└── specs/                       ← one directory per feature, created as you go
```

---

## 0.5 — Start your agent

Open your AI agent in this directory:

```bash
claude          # or: copilot, gemini, cursor, codex …
```

Confirm the commands registered by typing `/` and looking for the `speckit` entries. If they are missing, restart the agent — it needs to reload after `specify init`.

---

## ✅ Checkpoint

- [ ] `specify --version` works
- [ ] `specify check` reports your agent as available
- [ ] `.specify/` and `specs/` exist in the project
- [ ] `specify extension list` shows `git`
- [ ] Typing `/speckit` in your agent shows the command list

---

[Next: Module 1 — The Constitution & First Spec →](01-constitution-and-spec.md)
