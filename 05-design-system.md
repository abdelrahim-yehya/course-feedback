# Module 5 — Design System Constraints (Amending the Constitution)

**⏱ 20 minutes** · [← Module 4](04-csv-export.md) · [Next: Module 6 →](06-capstone.md)

---

## Goal

Make the UI genuinely usable while preventing the agent from reaching for a framework — and learn where a durable constraint belongs.

---

## 5.1 — The teaching point: constitution vs. spec

Left alone, an agent asked to "make this look modern" will often pull in Tailwind, Bootstrap, or a component library. Not out of malice — those are statistically what "modern UI" looks like in its training data.

The naive fix is to add "no frameworks" to the feature spec. **Wrong place.** Ask which is true:

- *"This feature's UI uses no CSS framework"* → a property of one feature
- *"This project uses no CSS framework, ever"* → a property of the project

The second is **constitutional**. Put it in a spec and you must restate it in every future spec, forever; the first time you forget, the constraint evaporates. Put it in the constitution and it's checked at every plan, for every feature, without you remembering anything.

---

## 5.2 — Amend the constitution

Interview first, again — because "modern and polished" is exactly the kind of phrase that means nothing until someone makes you define it.

### 👉 Prompt 1 — the interview (plain message)

```text
I want to add a design principle to our constitution. The UI has to look modern and polished, but it must be built only with hand-written CSS — no frameworks, no component libraries, no build step.

Interview me about what "modern and polished" should mean concretely: spacing, colour, elevation, interaction states, accessibility, responsiveness. Up to five questions with your recommendations. Also ask me what should be explicitly forbidden. Don't write anything yet.
```

**Answer key:**

| If it asks about… | We're going with |
|---|---|
| Colour and spacing values | Defined once as CSS custom properties on `:root`. No literal colour or spacing value anywhere else. |
| Spacing scale | 8px base. |
| Corners and elevation | 8px radius on cards, inputs, buttons. Layered subtle box shadows rather than heavy borders. |
| Interaction states | Every interactive element defines hover, focus-visible, active, and disabled. |
| Focus indicators | Always clearly visible. Never removed. Non-negotiable. |
| Contrast | WCAG AA for text on interactive and surface colours. |
| Layout | Grid and Flexbox. Usable down to 360px wide with no horizontal scroll. |
| Explicitly forbidden | CSS frameworks, resets, component libraries, icon fonts or packages, third-party web fonts, preprocessors, any build step. |
| Explicitly allowed | System font stacks, inline SVG. |

### 👉 Prompt 2 — generate

```text
/speckit.constitution Add what we just agreed as a new numbered design principle. Keep every existing principle intact and bump the document version.
```

**Open `.specify/memory/constitution.md`.** Version bumped, new principle present, earlier principles untouched. That amendment is now enforced across every future `/speckit.plan`.

---

## 5.3 — Specify the visual work

### 👉 Prompt 3

```text
/speckit.specify Make the app look and feel modern. No behaviour changes at all — same routes, same validation, same auth. Star rating instead of a number input. Feedback shown as cards with the course as a badge. Summary figures at the top of the dashboard. And every state properly designed: loading, empty, and error.
```

### 👉 Prompt 4

```text
/speckit.clarify
```

**Answer key:**

| If it asks about… | We're going with |
|---|---|
| Star rating interaction | Hover previews, click selects, selection visually obvious. Must work by keyboard. |
| Comment field | Live character count that changes appearance as it nears and exceeds 1000. |
| Submit button | Disabled until the form is valid. |
| After submitting | Confirmation that appears then fades; form resets. |
| Timestamps on cards | Human-readable and relative ("2 hours ago"). |
| Summary figures | Total count and average rating, for the current filter. |
| Loading state | Shown while feedback is being fetched. |
| Error state | Visible message region, not console-only. |

---

## 5.4 — Cascade

### 👉 Prompt 5

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

---

## 5.5 — Verify the constraint held

This is the real assessment of the module.

```bash
# Should list only your original dependencies
cat package.json

# Should return nothing
grep -rn "cdn\|unpkg\|jsdelivr\|googleapis\|tailwind\|bootstrap" public/ src/

# Should show a :root custom property block
head -40 public/*.css
```

Then in the browser: tab through the whole student form using only the keyboard. Can you set a rating? Is focus always visible? Resize to phone width — anything overflow?

**If the agent smuggled in a framework anyway,** that's a valuable moment. Don't rip it out by hand:

```text
This violates our constitution — you added [name it]. Remove it and rebuild that styling with hand-written CSS using the custom properties. Then tell me which artifact let this through: was the constitution ambiguous, or did the plan skip its constitution check?
```

The follow-up question is the important half. Violated constraints usually reveal a wording problem upstream, and the fix belongs there.

---

## 5.6 — Commit

### 👉 Prompt 6

```text
Check the diff, confirm tests pass and no dependencies were added, write a Conventional Commits message for the vanilla CSS design system, and commit. Don't push.
```

---

## ✅ Checkpoint

- [ ] `package.json` has no new dependencies
- [ ] No external CDN references in `public/` or `src/`
- [ ] Stylesheet driven by `:root` custom properties
- [ ] Star rating fully keyboard-operable with visible focus
- [ ] All previous tests still pass
- [ ] Fourth semantic commit exists

---

## 💬 Discussion (2 min)

> Which constraints in your own codebase are currently enforced only by reviewer memory? Those are the ones that decay silently — and they're exactly what a constitution is for.

---

[Next: Module 6 — Capstone →](06-capstone.md)
