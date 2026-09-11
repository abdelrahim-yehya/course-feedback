# PROMPTS.md — Every prompt, in order

Copy-paste reference for the whole workshop. Terminal commands are marked `bash`; everything else goes to your agent.

Every prompt here is short by design. **The detail comes from your answers**, not from the prompt. Each module in [`modules/`](modules/) carries the answer key for the questions the agent will ask.

> Substitute your agent's command form if it differs: `$speckit-specify` (Codex, ZCode) or `/skill:speckit-specify` (Kimi).

**Jump to:** [M0](#module-0) · [M1](#module-1) · [M2](#module-2) · [M3](#module-3) · [M4](#module-4) · [M5](#module-5) · [M6](#module-6) · [Recovery](#recovery-prompts)

---

## The two patterns

**Pattern A — interview, then command.** Used for the constitution and the plan, where you'd otherwise have to know every answer up front.

```text
Interview me before you write anything. Ask me the questions you need answered to do this well — up to five at a time, with your recommended default for each so I can say "defaults" where I don't care. When you have enough, summarise what you understood and wait for my confirmation before generating anything.
```

Then a one-line slash command referring back to the conversation.

**Pattern B — seed, then clarify.** Used for specifications. Short `/speckit.specify`, then `/speckit.clarify` repeatedly until nothing is ambiguous.

*Why not put "ask me questions" inside the slash command? Because the command template already instructs the agent to produce an artifact. Asked to do both in one turn, it generates. Separate turns are reliable.*

---

## Module 0

```bash
uv tool install specify-cli
mkdir course-feedback && cd course-feedback
specify init . --integration claude
specify extension add git
specify check
```

---

## Module 1

### 1 — Constitution interview *(plain message)*

```text
We're starting a new project: a small web app where students leave anonymous feedback on a course, and professors read that feedback on a private dashboard.

Before we write any spec, I want to agree the ground rules the whole project must follow — how we test, what dependencies we allow, how we handle security and privacy, and how we work in increments.

Interview me. Ask up to five questions at a time, give me your recommended default for each so I can just say "defaults" where I don't care, and don't write anything until I tell you to.
```

📋 [Answer key →](modules/01-constitution-and-spec.md#-prompt-1--open-the-interview-plain-message-not-a-slash-command)

### 2 — Generate the constitution

```text
/speckit.constitution Use the principles we just agreed on. Write them as numbered, non-negotiable principles, each with a one-line rationale, plus a governance clause stating that any deviation must be recorded in a specification with a written rationale before it is implemented.
```

### 3 — Seed the spec

```text
/speckit.specify Students leave anonymous feedback on a course: a rating from 1 to 5 and a written comment. Professors read all of it on a private dashboard that shows nothing at all to anyone without credentials. That's the whole first feature — no multiple courses, no export, no charts, no student accounts.
```

### 4 — Clarify

```text
/speckit.clarify
```
```text
/speckit.clarify Focus on anything still marked NEEDS CLARIFICATION.
```

📋 [Answer key →](modules/01-constitution-and-spec.md#14--let-the-tool-interrogate-you)

---

## Module 2

### 5 — Plan interview *(plain message)*

```text
We're ready to plan the technical implementation of this spec. Before you write the plan, interview me about the choices you need me to make — stack, storage, authentication, testing, and project layout. Up to five questions at a time, with your recommendation for each so I can accept defaults where I don't have an opinion. Don't write the plan yet.
```

📋 [Answer key →](modules/02-plan-tasks-implement.md#-prompt-1--the-interview-plain-message)

### 6 — Generate the plan

```text
/speckit.plan Use the stack and structure we just agreed. Keep it a single Node service with no build step, and keep the route handlers thin with logic in modules.
```

### 7 — Checklist

```text
/speckit.checklist Focus on validation, authentication boundaries, and empty or error states.
```

### 8–11 — Cascade

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

```bash
npm install && npm test && npm start
```

### 12 — Commit

```text
Check the diff, confirm the tests pass, then write a Conventional Commits message for the course feedback MVP and commit everything. Don't push.
```

---

## Module 3

### 13 — Specify

```text
/speckit.specify Feedback now belongs to a course. Three fixed courses: CS101, AI202, ENG304 — no way to add more at runtime. Students pick one when they submit; professors can filter the dashboard by course or see everything. Everything from the first feature keeps working exactly as it does now.
```

### 14 — Clarify

```text
/speckit.clarify
```

📋 [Answer key →](modules/03-multi-course.md#33--clarify)

### 15 — Plan

```text
/speckit.plan Extend what we already have — don't restructure the project. Add the course to the schema and to both API routes, with the course list in one shared module. Extend the Jest suite to cover a valid submission per course, rejection of a missing or unknown course, and filtered versus unfiltered retrieval.
```

### 16–19 — Cascade

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

### 20 — Commit

```text
Check the diff, confirm tests pass, write a Conventional Commits message for multi-course support and commit. Don't push.
```

---

## Module 4

### 21 — Specify (deliberately vague)

```text
/speckit.specify Professors can download all the feedback as a CSV from the dashboard, behind the same login as everything else. It has to survive whatever a student typed in the comment box — the file mustn't break, and opening it in a spreadsheet mustn't do anything dangerous. Also add Refresh and Download buttons to the dashboard.
```

### 22 — Make it sharpen the vague part

```text
/speckit.clarify Focus on what "the file mustn't break" and "mustn't do anything dangerous" have to mean precisely. Enumerate the specific cases.
```

📋 [Answer key →](modules/04-csv-export.md#42--let-it-sharpen-the-vague-part)

**If it doesn't raise formula injection:**

```text
What could go wrong if a student's comment starts with an equals sign and a professor opens the CSV in Excel?
```

### 23 — Checklist

```text
/speckit.checklist Focus on the CSV escaping and formula-injection requirements. Is every problematic character class covered, and is the expected output defined for each?
```

### 24 — Plan

```text
/speckit.plan Extend what we have. Put CSV generation in its own module with no Express dependency so it's unit-testable directly. Add a protected export route that takes the same course filter as the list route. Add the two dashboard buttons. Cover every escaping case we just enumerated in unit tests, plus route tests for auth, headers, and the filter.
```

### 25 — Tasks, analyze, phased implement, converge

```text
/speckit.tasks
```
```text
/speckit.analyze
```
```text
/speckit.implement Only the CSV generation module and its unit tests. Don't touch the routes or the frontend yet.
```
```text
/speckit.implement Now the export route, with its auth and header tests.
```
```text
/speckit.implement Now the Refresh and Download buttons, including the disabled-while-pending behaviour and error surfacing.
```
```text
/speckit.converge
```

### 26 — Manual verification

```bash
curl -i http://localhost:3000/api/feedback/export                           # expect 401
curl -i -u professor:yourpassword http://localhost:3000/api/feedback/export # expect 200 text/csv
```

### 27 — Commit

```text
Check the diff, confirm tests pass, write a Conventional Commits message for the CSV export and dashboard controls, mention the escaping and formula-injection protections in the body, and commit. Don't push.
```

---

## Module 5

### 28 — Design interview *(plain message)*

```text
I want to add a design principle to our constitution. The UI has to look modern and polished, but it must be built only with hand-written CSS — no frameworks, no component libraries, no build step.

Interview me about what "modern and polished" should mean concretely: spacing, colour, elevation, interaction states, accessibility, responsiveness. Up to five questions with your recommendations. Also ask me what should be explicitly forbidden. Don't write anything yet.
```

📋 [Answer key →](modules/05-design-system.md#-prompt-1--the-interview-plain-message)

### 29 — Amend the constitution

```text
/speckit.constitution Add what we just agreed as a new numbered design principle. Keep every existing principle intact and bump the document version.
```

### 30 — Specify

```text
/speckit.specify Make the app look and feel modern. No behaviour changes at all — same routes, same validation, same auth. Star rating instead of a number input. Feedback shown as cards with the course as a badge. Summary figures at the top of the dashboard. And every state properly designed: loading, empty, and error.
```

### 31 — Clarify

```text
/speckit.clarify
```

📋 [Answer key →](modules/05-design-system.md#-prompt-4)

### 32 — Plan and cascade

```text
/speckit.plan Restyle the existing frontend only — server code and API contracts unchanged. Rebuild the stylesheet around a :root custom property block. Build the star rating from accessible radio inputs styled with CSS, so keyboard operation and screen-reader semantics come for free instead of being rebuilt in JavaScript. Existing tests must keep passing unmodified.
```
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

### 33 — Verify the constraint held

```bash
cat package.json
grep -rn "cdn\|unpkg\|jsdelivr\|googleapis\|tailwind\|bootstrap" public/ src/
head -40 public/*.css
```

### 34 — Commit

```text
Check the diff, confirm tests pass and no dependencies were added, write a Conventional Commits message for the vanilla CSS design system, and commit. Don't push.
```

---

## Module 6

### 35 — The exception conversation *(plain message)*

```text
I want to use Chart.js from a CDN for a chart on the dashboard. Our constitution forbids both new dependencies and third-party CDN assets.

Don't just do it, and don't just refuse. Walk me through my options for handling this properly, then ask me what scope the exception should have if we grant one.
```

📋 [Answer key →](modules/06-capstone.md#61--the-governed-exception)

### 36 — Record the exception

```text
/speckit.constitution Record that exception exactly as we scoped it, in a new "Authorised Exceptions" section. Keep every existing principle intact and bump the version.
```

### 37 — Specify

```text
/speckit.specify Feedback has to survive a server restart. And the dashboard gets a chart showing the average rating per course. Everything else stays exactly as it is.
```

### 38 — Clarify

```text
/speckit.clarify Focus on storage lifecycle and on how the chart should handle courses with no feedback.
```

📋 [Answer key →](modules/06-capstone.md#63--clarify--including-the-question-a-production-system-would-force-on-you)

### 39 — Plan

```text
/speckit.plan Extend what we have. File-backed SQLite with a configurable path, gitignored, schema initialised idempotently at startup. Add a protected stats route returning count and average per course. Load Chart.js on the dashboard page only, integrity-pinned, styled with our existing CSS custom properties, with the same numbers rendered as text alongside it. Tests must use an isolated database.
```

### 40 — Tasks, analyze, phased implement, converge

```text
/speckit.tasks
```
```text
/speckit.analyze
```
```text
/speckit.implement Only the persistence work: file-backed database, configurable path, gitignore, idempotent schema init, and the persistence tests. Not the stats route or the chart yet.
```
```text
/speckit.implement Now the stats route and its tests.
```
```text
/speckit.implement Now the chart and the text summary.
```
```text
/speckit.converge
```

### 41 — Final commit

```text
Check the diff, confirm database.sqlite isn't staged and the tests pass, write a Conventional Commits message for the persistence and chart work, and commit. Don't push.
```

---

## Recovery prompts

Keep these to hand. They're the ones you'll actually reach for in a room of 20 people.

### The agent generated instead of interviewing

```text
Stop — I asked you to interview me first. Discard what you just wrote and ask me your questions instead.
```

### It's asking too many questions, or trivial ones

```text
Take your best guess on anything minor and just tell me what you assumed. Only ask me about decisions that would be expensive to reverse later.
```

### You want to move fast

```text
Defaults for all of those.
```

### Tests are failing

```text
The test suite is failing. Diagnose the root cause. If it's a defect in the implementation, fix it. If it's because the spec or plan was ambiguous or wrong, tell me which artifact is at fault and what it should say instead — don't silently work around it.

[paste the output]
```

### The agent violated the constitution

```text
This violates our constitution — you added [name it]. Remove it and rebuild in compliance. Then tell me which artifact let this through: was the constitution ambiguous, or did the plan skip its constitution check?
```

### It went off-script and rewrote too much

```text
You changed files outside the scope of the current tasks. List everything you modified that isn't referenced by a task in tasks.md, revert those changes, re-run the tests, and tell me which tasks are still incomplete.
```

### An earlier feature regressed

```text
Something we specified in an earlier feature stopped working: [describe]. Find the requirement, write a failing test that reproduces it, then fix the code so both the old and new specs are satisfied.
```

### `/speckit.implement` stalled or ran out of context

```text
Tell me which tasks in tasks.md are actually complete based on the current state of the codebase, not on what you remember doing. Then implement only the next incomplete phase.
```

### You need to know where you are

```bash
cat .specify/feature.json     # which feature is active
specify extension list        # what's installed
git log --oneline             # what's committed
```
