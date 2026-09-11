# Module 6 — Capstone: Persistence & Visual Analytics

**⏱ 20 minutes** · [← Module 5](05-design-system.md) · [Wrap-up →](../INSTRUCTOR-NOTES.md#wrap-up)

---

## Goal

Two hard things at once, both of which have bitten every engineer in the room before:

1. **Moving from ephemeral to persistent storage** — file paths, `.gitignore`, schema initialisation, and the migration question
2. **Authorising a dependency against a constitution that forbids dependencies** — the governed exception

---

## 6.1 — The governed exception

We want Chart.js from a CDN. Our constitution forbids both new dependencies and third-party CDN assets.

Rather than telling the agent what to do, ask it to lay out the options. This models something worth modelling: **when you hit your own rules, you deliberate, you don't improvise.**

### 👉 Prompt 1 (plain message)

```text
I want to use Chart.js from a CDN for a chart on the dashboard. Our constitution forbids both new dependencies and third-party CDN assets.

Don't just do it, and don't just refuse. Walk me through my options for handling this properly, then ask me what scope the exception should have if we grant one.
```

**The three options it should surface** — and the discussion:

| Option | Consequence |
|---|---|
| Ignore the constitution, just add it | The constitution becomes decorative. Do this once and it means nothing. |
| Weaken the principle to allow CDN dependencies generally | Loses the protection entirely to solve one case |
| **Record a narrow, named exception with a rationale** | Constraint holds; the exception is auditable |

We take the third — which is exactly what the governance clause from Module 1 pre-authorised.

**Answer key for the scoping questions:**

| If it asks about… | We're going with |
|---|---|
| What's authorised | Chart.js, by name, and nothing else. |
| Where | The professor dashboard only. The public student form stays free of third-party assets. |
| How it's loaded | Versioned CDN URL with a subresource integrity hash and `crossorigin`. Not in `package.json`. |
| Rationale | Accessible charting from scratch in canvas costs far more than it's worth, and this is one authenticated internal page. |
| Does it weaken anything else | No. Any future exception needs its own amendment. |

### 👉 Prompt 2

```text
/speckit.constitution Record that exception exactly as we scoped it, in a new "Authorised Exceptions" section. Keep every existing principle intact and bump the version.
```

Read the result. Every future plan is now checked against a rule set that permits exactly one dependency, for one page, for a written reason. That's what governance looks like when it's machine-readable.

---

## 6.2 — Specify the capstone

### 👉 Prompt 3

```text
/speckit.specify Feedback has to survive a server restart. And the dashboard gets a chart showing the average rating per course. Everything else stays exactly as it is.
```

---

## 6.3 — Clarify — including the question a production system would force on you

### 👉 Prompt 4

```text
/speckit.clarify Focus on storage lifecycle and on how the chart should handle courses with no feedback.
```

**Answer key:**

| If it asks about… | We're going with |
|---|---|
| Where the data lives | A `database.sqlite` file, gitignored, never committed. Path configurable via `DATABASE_PATH`. |
| First run with no data | Initialises automatically and starts empty. Idempotent `CREATE TABLE IF NOT EXISTS`. |
| Existing DB with an older schema | **Out of scope — but say so explicitly in the spec.** Documented recovery: delete the file. |
| Tests touching the real database | Never. Isolated temp file per run, cleaned up. |
| A course with no feedback | Shown as *no data*, not as zero. No feedback and an average of zero are different facts. |
| Chart axis | Fixed 1–5, so bars are comparable between views. |
| Does the chart respect the course filter | No — always shows all three, so professors can compare. The list stays filtered. |
| If Chart.js fails to load | Page still works. The same averages are also rendered as text. |

Two of those rows deserve airtime:

**"Out of scope" is a decision.** Undefined behaviour is how you get a 3am incident. *Defined-and-deliberately-excluded* is engineering. Make the agent write it into the spec.

**The text fallback** isn't a nicety — it's the requirement that makes an external chart library a safe dependency rather than a single point of failure.

---

## 6.4 — Plan and implement in phases

### 👉 Prompt 5

```text
/speckit.plan Extend what we have. File-backed SQLite with a configurable path, gitignored, schema initialised idempotently at startup. Add a protected stats route returning count and average per course. Load Chart.js on the dashboard page only, integrity-pinned, styled with our existing CSS custom properties, with the same numbers rendered as text alongside it. Tests must use an isolated database.
```

```text
/speckit.tasks
```
```text
/speckit.analyze
```

### 👉 Prompt 6 — persistence first

```text
/speckit.implement Only the persistence work: file-backed database, configurable path, gitignore, idempotent schema init, and the persistence tests. Not the stats route or the chart yet.
```

```bash
npm test
npm start
# submit feedback, Ctrl+C, then start again — it should still be there
npm start
git status --short   # database.sqlite must NOT appear
```

### 👉 Prompts 7 & 8

```text
/speckit.implement Now the stats route and its tests.
```
```text
/speckit.implement Now the chart and the text summary.
```

---

## 6.5 — Converge to done

```text
/speckit.converge
```

Loop `/speckit.implement` → `/speckit.converge` until it reports converged. Watch the appended-task count shrink each pass. When it comes back clean, the codebase demonstrably satisfies the specification — a stronger claim than "the tests pass."

---

## 6.6 — Final commit

### 👉 Prompt 9

```text
Check the diff, confirm database.sqlite isn't staged and the tests pass, write a Conventional Commits message for the persistence and chart work, and commit. Don't push.
```

Then look at what you built:

```bash
git log --oneline
```

Six commits. Every one traceable to a specification. Not one line of application code typed by hand — and not one prompt longer than a short paragraph.

---

## ✅ Final checkpoint

- [ ] Feedback survives a server restart
- [ ] `database.sqlite` gitignored and never staged
- [ ] Tests run against an isolated database
- [ ] Chart shows average per course, axis fixed 1–5
- [ ] Courses with no feedback show as no-data, not zero
- [ ] Text summary present and correct with the chart blocked
- [ ] Chart.js the only external asset, dashboard only
- [ ] `/speckit.converge` reports converged
- [ ] Six semantic commits

---

## 🏁 Stretch goals

1. **Break it on purpose.** Hand-edit one requirement in `spec.md`, run `/speckit.analyze`, watch it catch the inconsistency.
2. **Regenerate from scratch.** Clone to a new directory, delete `src/` and `public/`, run `/speckit.implement` against the same specs. How close is it? That divergence is the honest measure of how complete your specs really are.
3. **Try a longer prompt for comparison.** Write one feature the old way — a single 300-word spec prompt with no clarify. Compare the resulting `spec.md` with one built through dialogue. Which is better, and which took less effort?
4. **Point it at real work.** See [`INSTRUCTOR-NOTES.md`](../INSTRUCTOR-NOTES.md#adopting-this-on-an-existing-codebase).

---

[Wrap-up & discussion →](../INSTRUCTOR-NOTES.md#wrap-up)
