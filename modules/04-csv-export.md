# Module 4 — Secure CSV Export & Dashboard Controls

**⏱ 25 minutes** · [← Module 3](03-multi-course.md) · [Next: Module 5 →](05-design-system.md)

---

## Goal

Two techniques:

1. **Handing the agent a deliberately vague requirement and letting it sharpen the edges.** CSV looks trivial and isn't commas, quotes, newlines, formula injection. You shouldn't have to know that list in advance. You should have to *recognise it when the agent offers it.*
2. **Phased implementation** — scoping `/speckit.implement` to one slice at a time.

---

## 4.1 — Specify, vaguely and on purpose

### 👉 Prompt 1

```text
/speckit.specify Professors can download all the feedback as a CSV from the dashboard, behind the same login as everything else. It has to survive whatever a student typed in the comment box — the file mustn't break, and opening it in a spreadsheet mustn't do anything dangerous. Also add Refresh and Download buttons to the dashboard.
```

Read that back. *"Mustn't break"* and *"mustn't do anything dangerous"* are not requirements, they're gestures at requirements. A human reviewer would push back on them. So will the agent, which is the point.

---

## 4.2 — Let it sharpen the vague part

### 👉 Prompt 2

```text
/speckit.clarify Focus on what "the file mustn't break" and "mustn't do anything dangerous" have to mean precisely. Enumerate the specific cases.
```

**What a good agent comes back with** and what to confirm:

| It should raise | Answer |
|---|---|
| Comments containing commas | Quote the field. RFC 4180. |
| Comments containing double quotes | Escape by doubling, inside quotes. |
| Comments containing line breaks | Quote the field; the row must not split. |
| Comments starting with `=`, `+`, `-`, `@`, tab, CR | Neutralise it — a spreadsheet must never execute it as a formula. |
| Whether export respects the active course filter | Yes. Viewing one course exports one course. |
| Filename | Descriptive, includes the date and the course filter when one is active. |
| Download vs display in browser | A real file download, not rendered in the tab. |
| Unauthenticated request | Challenged, zero data. Same as everything else. |
| Empty result set | Header row only, not an empty file. |
| Button behaviour while a request is in flight | Disabled, so they can't be double-fired. Errors surface visibly, not just in the console. |


### 👉 Prompt 3 — validate the requirements

```text
/speckit.checklist Focus on the CSV escaping and formula-injection requirements. Is every problematic character class covered, and is the expected output defined for each?
```

---

## 4.3 — Plan

### 👉 Prompt 4

```text
/speckit.plan Extend what we have. Put CSV generation in its own module with no Express dependency so it's unit-testable directly. Add a protected export route that takes the same course filter as the list route. Add the two dashboard buttons. Cover every escaping case we just enumerated in unit tests, plus route tests for auth, headers, and the filter.
```

---

## 4.4 — Tasks, then implement in phases

```text
/speckit.tasks
```
```text
/speckit.analyze
```

### 👉 Prompt 5 — phase 1

```text
/speckit.implement Only the CSV generation module and its unit tests. Don't touch the routes or the frontend yet.
```

```bash
npm test
```

Read the escaping tests. Are the edge cases actually asserted? This is the moment to catch a lazy implementation while it's five files, not fifty.

### 👉 Prompt 6 — phase 2

```text
/speckit.implement Now the export route, with its auth and header tests.
```

```bash
npm test
```

### 👉 Prompt 7 — phase 3

```text
/speckit.implement Now the Refresh and Download buttons, including the disabled-while-pending behaviour and error surfacing.
```

```text
/speckit.converge
```

---

## 4.5 — Verify by hand

Worth doing properly — this is security-relevant.

```bash
npm start
```

1. Submit feedback containing a comma, a double quote, and a line break
2. Submit one whose comment begins with `=1+1`
3. `curl -i http://localhost:3000/api/feedback/export` → expect **401**, no data
4. `curl -i -u professor:yourpassword http://localhost:3000/api/feedback/export` → expect **200**, `text/csv`, header row
5. Download from the dashboard, open in a spreadsheet — columns intact, no formula executed

---

## 4.6 — Commit

### 👉 Prompt 8

```text
Check the diff, confirm tests pass, write a Conventional Commits message for the CSV export and dashboard controls, mention the escaping and formula-injection protections in the body, and commit. Don't push.
```

---

## ✅ Checkpoint

- [ ] Export returns 401 without credentials
- [ ] CSV survives commas, quotes, and newlines
- [ ] Leading `=` is neutralised
- [ ] Refresh re-renders without reload; both buttons disable while pending
- [ ] Third semantic commit exists

---


[Next: Module 5 — Design System Constraints →](05-design-system.md)
