# Module 0 — Setup & Orientation

**⏱ 15 minutes** · [← Back to README](../README.md) · [Next: Module 1 →](01-constitution-and-spec.md)

---

## Goal

Everyone has Spec Kit installed, a project initialized, an agent connected, and knows what the scaffold contains.

---

## 0.1 — Install the prerequisites

If you did the pre-work, skip to 0.2.

```bash
# uv (macOS / Linux)
curl -LsSf https://astral.sh/uv/install.sh | sh

# uv (Windows PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# verify
node --version    # expect v20+
git --version
uv --version
```

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

For a workshop, pinning a version is worth the extra 15 seconds. It means everyone sees the same prompts and the same generated templates.
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

**Two things to point out to the room:**

**1. The slash commands are just prompt files.** Open one — `.claude/commands/speckit.specify.md` or the equivalent for your agent. It is a markdown prompt. There is no magic, no hosted service, no model fine-tuned on your repo. Spec Kit is a well-engineered set of prompts plus a directory convention. That is genuinely reassuring for a security-conscious team, and it means you can read and fork any of it.

This also explains a technique used throughout the workshop: because each command is a template that ends with "now produce the artifact", asking it to interview you in the same message tends to lose. We do the interview as its own turn instead.

**2. Feature state lives in `.specify/feature.json`, not in your git branch.** Commands resolve the active feature from that file. Checking out a different branch does **not** change which feature the agent is working on. If you need to point at a different feature, edit `.specify/feature.json` or set `SPECIFY_FEATURE_DIRECTORY`. This trips people up, so flag it early.

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
