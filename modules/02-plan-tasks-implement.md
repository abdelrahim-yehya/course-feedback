# Module 2 — Plan → Checklist → Tasks → Analyze → Implement → Converge

**⏱ 35 minutes** · [← Module 1](01-constitution-and-spec.md) · [Next: Module 3 →](03-multi-course.md)

---

## Goal

Take the spec through the full quality-gated cascade to a running, tested MVP. This is the longest module and the one where the process either earns trust or loses it.

---

## 2.1 — `/speckit.plan` — the technical design

Now, and only now, the tech stack. Same technique as the constitution: interview first, generate second.

### 👉 Prompt 1 — the interview (plain message)

```text
We're ready to plan the technical implementation of this spec. Before you write the plan, interview me about the choices you need me to make — stack, storage, authentication, testing, and project layout. Up to five questions at a time, with your recommendation for each so I can accept defaults where I don't have an opinion. Don't write the plan yet.
```

**Answer key:**

| If it asks about… | We're going with |
|---|---|
| Runtime & framework | Node.js with Express. Single service, **no build step**, no bundler, no transpiler. |
| Database | SQLite — `node:sqlite` built-in or `better-sqlite3`, agent's choice. In-memory is fine for this feature. |
| API shape | `POST /api/feedback` (public) and `GET /api/feedback` (protected), plus static file serving. |
| Auth mechanism | Basic Auth as Express middleware, reading `PROFESSOR_USER` / `PROFESSOR_PASS` from env, with a documented `.env.example`. |
| Frontend | Two static pages — `index.html`, `dashboard.html`. Vanilla JS with `fetch`, one plain CSS file. |
| Testing | Jest plus supertest for HTTP-level route tests. `npm test` runs the suite, `npm start` runs the server. |
| Project layout | `src/` for code, `public/` for static assets, `tests/` mirroring source. |
| Where validation lives | Its own module, so it's unit-testable independently of the routes. |
| Schema details | `id`, `rating`, `comment`, `created_at`. Let the agent propose types. |

If the agent proposes something better than what's in this table, take it — and say why out loud. That's the workshop working.

### 👉 Prompt 2 — generate

```text
/speckit.plan Use the stack and structure we just agreed. Keep it a single Node service with no build step, and keep the route handlers thin with logic in modules.
```

**Read `plan.md`, `data-model.md`, and the contracts directory.** Two things to check as a group:

1. **Did the plan honour the constitution?** Most plan templates include a constitution-check gate — find it. If the agent had proposed React here, this gate is where it should have caught itself.
2. **Are there decisions you disagree with?** This is the cheapest possible moment to change them. Editing `plan.md` now costs nothing; changing it after `/implement` costs a regeneration.

---

## 2.2 — `/speckit.checklist` — unit tests for your requirements

Before breaking work down, validate the requirements themselves. This generates a review checklist that tests the **spec**, not the code: *is every rule defined for every case? is behaviour specified when X is absent?*

### 👉 Prompt 3

```text
/speckit.checklist Focus on validation, authentication boundaries, and empty or error states.
```

Open the generated checklist under `specs/001-*/checklists/`. Work through it as a reviewer, tick an item only when you're satisfied the requirement genuinely covers it. If an item exposes a gap, go back:

```text
/speckit.clarify Focus on the gaps the checklist surfaced.
```

---

## 2.3 — `/speckit.tasks` — the executable breakdown

### 👉 Prompt 4

```text
/speckit.tasks
```

No arguments — everything it needs is in the artifacts.

**Open `tasks.md`.** This is the most persuasive file in the toolkit. Point out:

- **Phases**: Setup → Foundational (blocking prerequisites) → one phase per user story in priority order → Polish
- **Dependency ordering** — tasks are sequenced, not listed
- **`[P]` markers** — tasks that can safely run in parallel
- **Test tasks interleaved within each user story**, not bolted on at the end. That's the constitution's Test-First principle showing up as structure.
- Each task references the requirement it satisfies

Count them. Typically 25–40. Ask: *how long would writing this breakdown by hand have taken?*

---

## 2.4 — `/speckit.analyze` — the consistency gate

A read-only pass across `spec.md`, `plan.md`, and `tasks.md` looking for conflicts, gaps, and orphans: a task with no matching requirement, a plan choice that contradicts the spec, a constitutional violation.

### 👉 Prompt 5

```text
/speckit.analyze
```

It writes nothing. It produces a report.

**When it flags something, fix it at the source, not in the report:**

| Problem type | Go back to |
|---|---|
| A requirement is missing, vague, or contradictory | `/speckit.clarify` |
| A design or stack decision is wrong | `/speckit.plan` |
| The breakdown doesn't match the design | `/speckit.tasks` (regenerate) |

Re-run `/speckit.analyze` until clean. This discipline — *fix it where it's owned* — is the single most transferable habit in the workshop. Patching downstream artifacts is how spec-driven projects rot back into ordinary ones.

---

## 2.5 — `/speckit.implement` — build it

### 👉 Prompt 6

```text
/speckit.implement
```

The agent now works through `tasks.md` in dependency order: failing test, minimum code to pass, verify, next.

### Verify it actually works

```bash
npm install
npm test
npm start
```

Then in a browser:

- `http://localhost:3000` — submit feedback
- `http://localhost:3000/dashboard.html` — expect a Basic Auth challenge, then your submission

**If tests fail, do not open the editor.** Hand the failure back as evidence:

```text
The test suite is failing. Diagnose the root cause. If it's a defect in the implementation, fix it. If it's because the spec or plan was ambiguous or wrong, tell me which artifact is at fault and what it should say instead. Don't silently work around it.

[paste the output]
```

That second sentence preserves the invariant that the spec is the source of truth, and surfaces the case where the real bug is upstream.

---

## 2.6 — `/speckit.converge` — did we build what we specified?

`/speckit.implement` finishing is not the same as the feature being complete. `/speckit.converge` assesses the **codebase** against the spec, plan, and tasks. It's append-only: never edits code, and its only possible write is adding tasks to `tasks.md`.

### 👉 Prompt 7

```text
/speckit.converge
```

Two outcomes:

- **✅ Converged** — no gaps. `tasks.md` untouched. Done.
- **Tasks appended** — gaps found, added under a Convergence section. Run `/speckit.implement` again, then converge again. Each pass finds fewer items.

Loop until converged. This is the closest thing SDD has to a definition of done, and it's a machine-checkable one.

---

## 2.7 — Commit the MVP

### 👉 Prompt 8

```text
Check the diff, confirm the tests pass, then write a Conventional Commits message for the course feedback MVP and commit everything. Don't push.
```

Check with `git log -1` and `git show --stat`. If the message is vague, ask for a better one — the agent has full context and commit messages are documentation.

---

## ✅ Checkpoint

- [ ] `npm test` passes
- [ ] Student form stores feedback; dashboard requires auth and displays it
- [ ] `/speckit.analyze` reported clean
- [ ] `/speckit.converge` reported converged
- [ ] One semantic commit exists
---

[Next: Module 3 — Multi-Course Support →](03-multi-course.md)
