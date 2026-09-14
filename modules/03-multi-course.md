# Module 3 — Multi-Course Support (The Mid-Flight Change)

**⏱ 25 minutes** · [← Module 2](02-plan-tasks-implement.md) · [Next: Module 4 →](04-csv-export.md)

---

## Goal

Handle the requirement change that arrives after you've shipped, the one touching the schema, the API, and both frontends, without opening the editor.

**New requirement:** feedback must be attributed to one of three courses (`CS101`, `AI202`, `ENG304`). Students choose a course; professors filter by it.

---

## 3.1 — First, decide: new feature or amended spec?

Worth 60 seconds before you type anything.

| | New feature (new spec directory) | Amend the existing spec |
|---|---|---|
| **When** | Additive capability; increment reviewable on its own | The original requirement was wrong |
| **Result** | `specs/002-multi-course/`, own branch, own PR | `specs/001-*/spec.md` rewritten in place |
| **History** | Preserved — you can see what shipped when | Rewritten — the record now says it was always this way |

**We'll use a new feature.** It maps to how you'd actually ship this, and it demonstrates that Spec Kit handles a growing feature set rather than one ever-mutating document.

> Remember: the active feature is tracked in `.specify/feature.json`, not by your git branch.

---

## 3.2 — Specify the change

### 👉 Prompt 1

```text
/speckit.specify Feedback now belongs to a course. Three fixed courses: CS101, AI202, ENG304, no way to add more at runtime. Students pick one when they submit; professors can filter the dashboard by course or see everything. Everything from the first feature keeps working exactly as it does now.
```

That last sentence is the important one. **Stating what must not change is as important as stating what must.** Without it, a regenerated implementation can quietly drop a constraint from an earlier feature. This is the single most useful habit for iterative SDD, and it's six words.

---

## 3.3 — Clarify

### 👉 Prompt 2

```text
/speckit.clarify
```

**Answer key:**

| If it asks about… | We're going with |
|---|---|
| Default selection on the form | None. No pre-selected course, so nobody submits against the wrong one by accident. |
| Required or optional | Required. Missing or unknown course is rejected server-side, nothing stored. |
| Filter default | All courses. |
| Does filtering reload the page | No. Re-fetch and re-render with JS. |
| Filter matching nothing | Empty state that names the filtered course. |
| Filter persistence across reloads | No. Reset to "all". |
| Existing rows without a course | Not a concern — in-memory DB starts empty. **Flag this out loud:** in Module 6 it becomes a real migration question. |
| Where the course list is defined | One shared module, read by both the server validator and the frontend. Three codes, one place. |

That last row is a decision the agent may not think to raise. If it doesn't ask, tell it anyway, it's the difference between a maintainable change and three hardcoded lists.

---

## 3.4 — Plan

### 👉 Prompt 3

```text
/speckit.plan Extend what we already have — don't restructure the project. Add the course to the schema and to both API routes, with the course list in one shared module. Extend the Jest suite to cover a valid submission per course, rejection of a missing or unknown course, and filtered versus unfiltered retrieval.
```

---

## 3.5 — Cascade

```text
/speckit.tasks
```
```text
/speckit.analyze
```
```text
/speckit.implement
```
```text
/speckit.converge
```

Then verify:

```bash
npm test
npm start
```

Submit feedback for two different courses, then filter the dashboard by each.

---

## 3.6 — Commit

### 👉 Prompt 4

```text
Check the diff, confirm tests pass, write a Conventional Commits message for multi-course support and commit. Don't push.
```

---

## ✅ Checkpoint

- [ ] Course selector on the form, required, no default
- [ ] Unknown or missing course rejected server-side
- [ ] Dashboard filter works without a page reload
- [ ] Earlier tests still pass — nothing regressed
- [ ] Second semantic commit exists

---

[Next: Module 4 — Secure CSV Export →](04-csv-export.md)
