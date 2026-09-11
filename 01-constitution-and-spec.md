# Module 1 — The Constitution & The First Spec

**⏱ 25 minutes** · [← Module 0](00-setup.md) · [Next: Module 2 →](02-plan-tasks-implement.md)

---

## Goal

Establish the project's non-negotiable principles, then write a specification that describes *what* the product does without leaking *how* it is built — using short prompts and letting the agent do the interrogating.

---

## 1.1 — Why the constitution comes first

The constitution (`.specify/memory/constitution.md`) holds the rules that every later phase is evaluated against: testing discipline, architectural constraints, security posture, dependency policy.

It exists because of a failure mode you will otherwise hit repeatedly: **restating the same constraint in every spec.** "Use TDD." "No frameworks." "Auth required." Ten specs later, one of them omits a line and the agent quietly does something else.

| Constitution | Spec |
|---|---|
| True for the lifetime of the project | True for this feature |
| "All state-changing routes require auth" | "The dashboard shows a course filter" |
| "No frontend frameworks" | "Ratings are 1–5 stars" |
| Changing it is a team decision | Changing it is a normal iteration |

This is the piece most teams skip, and it is the piece that determines whether SDD holds up past feature three.

---

## 1.2 — The Interview Rule

Here is the technique you'll reuse all afternoon. Don't try to write the constitution. Make the agent extract it from you.

### 👉 Prompt 1 — open the interview (plain message, **not** a slash command)

```text
We're starting a new project: a small web app where students leave anonymous feedback on a course, and professors read that feedback on a private dashboard.

Before we write any spec, I want to agree the ground rules the whole project must follow — how we test, what dependencies we allow, how we handle security and privacy, and how we work in increments.

Interview me. Ask up to five questions at a time, give me your recommended default for each so I can just say "defaults" where I don't care, and don't write anything until I tell you to.
```

**Answer the questions.** This is the part that matters. Here's the answer key so the room stays aligned — if the agent asks something not on this list, decide for yourself and say so out loud:

| If it asks about… | We're going with |
|---|---|
| Testing approach | Strict TDD. Failing Jest test first, always. Happy path **and** a failure path for every route. |
| Dependencies | Closed set. Nothing added unless a spec names it explicitly. No frontend or CSS frameworks — vanilla JS and CSS. |
| Auth | Any route that reads or exports feedback requires HTTP Basic Auth. Credentials from env vars, never committed, never logged. |
| Privacy | Submissions are anonymous. No names, emails, IPs, or identifiers stored — ever. |
| Input handling | Validate server-side before the database. Parameterised queries only. |
| Increments | One feature = one spec = one commit. Conventional Commits. Tests green before committing. |
| What happens when a rule must be broken | It gets written down in the spec with a rationale, **before** implementation. |

That last row is worth pausing on. You are pre-authorising a process for breaking your own rules. Module 6 cashes it in.

### 👉 Prompt 2 — generate it

```text
/speckit.constitution Use the principles we just agreed on. Write them as numbered, non-negotiable principles, each with a one-line rationale, plus a governance clause stating that any deviation must be recorded in a specification with a written rationale before it is implemented.
```

**Open `.specify/memory/constitution.md` and read it.** The agent has turned a five-minute conversation into a versioned, structured document that every subsequent command is checked against.

> **Compare with the alternative.** Some tutorials have you paste a 400-word constitution. That works too — but only if you already knew all seven things to say. The interview gets you there without knowing, and it surfaces the questions you'd have forgotten.

---

## 1.3 — Writing a specification

One rule dominates this step:

> **The spec describes behaviour, not technology.** No Express. No SQLite. No Jest. Those go in `/speckit.plan`.

This separation is not bureaucratic. It exists so requirements survive a change of stack, and so that reviewing *what we're building* isn't tangled up with *how*. If your spec mentions a library name, you've written a plan.

### 👉 Prompt 3 — seed the spec (short, deliberately)

```text
/speckit.specify Students leave anonymous feedback on a course: a rating from 1 to 5 and a written comment. Professors read all of it on a private dashboard that shows nothing at all to anyone without credentials. That's the whole first feature — no multiple courses, no export, no charts, no student accounts.
```

Four sentences. Notice what it contains: the two audiences, the shape of the data, the one hard security property, and an explicit scope boundary. Notice what it *doesn't* contain: validation rules, ordering, empty states, error handling. Those are coming — from the agent, not from you.

**Open `spec.md` and read it as a group.** Look for:

- **User stories, prioritised** (P1, P2, P3) — your four sentences decomposed into independently testable slices
- **Numbered functional requirements** — what tasks will trace back to
- **`[NEEDS CLARIFICATION]` markers** — genuine ambiguities the agent refused to invent answers for. That refusal is a feature.

Ask the room: *what did it invent that you didn't say?* Usually plenty. That's what the next step is for.

---

## 1.4 — Let the tool interrogate you

`/speckit.clarify` asks up to five targeted questions about underspecified areas and writes your answers back into `spec.md`. It's the cheapest quality gate in the process: answering here costs 20 seconds, discovering the same gap after implementation costs 20 minutes.

### 👉 Prompt 4 — broad pass, no arguments

```text
/speckit.clarify
```

**Answer key:**

| If it asks about… | We're going with |
|---|---|
| Invalid submissions | Reject with a clear message saying what to fix. Store nothing. Missing rating, rating outside 1–5, empty comment, or comment over 1000 chars. |
| Where errors appear | Inline in the form, with the typed comment preserved. |
| After a successful submit | Confirmation message, form resets, ready for another. |
| Ordering on the dashboard | Newest first. |
| Pagination | None this feature. Show everything. |
| Empty dashboard | An explanatory empty state, not a blank page. |
| Timestamps | Stored ISO 8601, displayed human-readably. |
| Credentials | One shared professor username and password, from environment variables. |
| What an unauthenticated visitor sees | A challenge, and zero feedback content. Not a redirect to a page that leaks counts. |

### 👉 Prompt 5 — mop up

```text
/speckit.clarify Focus on anything still marked NEEDS CLARIFICATION.
```

Repeat until clean.

**Re-open `spec.md`.** Your answers are now requirements, numbered and version-controlled. You've just done requirements engineering in five minutes, from four sentences, and there's a written record of every decision.

---

## ✅ Checkpoint

- [ ] `.specify/memory/constitution.md` has numbered principles and a governance clause
- [ ] `specs/001-*/spec.md` exists with prioritised user stories and numbered requirements
- [ ] No tech stack names appear anywhere in `spec.md`
- [ ] No `[NEEDS CLARIFICATION]` markers remain

---

## 💬 Discussion (2 min)

> Compare your `spec.md` with the person next to you. Same four-sentence seed, different conversations — how far apart did you end up? That gap is the honest measure of how much the dialogue is doing, and it's the thing to be careful about when two people on your team specify adjacent features.

---

[Next: Module 2 — Plan → Tasks → Implement →](02-plan-tasks-implement.md)
