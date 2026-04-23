---
name: create-ticket
description:
  Create one or more GitHub issues in Neovaude/neovaude-website and add them to
  the "Neovaude Website" project (#6) with all fields set. Developer provides a
  brief freetext description; Claude generates the full ticket body, asks
  clarifying questions, previews, confirms, then executes.
user-invocable: true
---

# /create-ticket

Create GitHub issues in `Neovaude/neovaude-website` from a brief description.
Supports single and bulk tickets.

---

## Fixed values (never prompt for these)

| Setting  | Value                                                                                                   |
| -------- | ------------------------------------------------------------------------------------------------------- |
| Repo     | `Neovaude/neovaude-website`                                                                             |
| Project  | **Neovaude Website** — org `Neovaude`, number `6`                                                       |
| Figma    | `https://www.figma.com/design/fRzrn1dNzAi5rvpMdShvyK/NEOVAUDE-WEBSITE?node-id=0-1&t=mUIFO8DFJGUa9NQY-1` |
| Status   | Always `Todo` (field id `339282918`, option `f75ad846`)                                                 |
| Assignee | Leave blank                                                                                             |
| Epic     | Leave blank                                                                                             |

### Label mapping

| Ticket type                        | Label     |
| ---------------------------------- | --------- |
| Functional feature                 | `feature` |
| Bug                                | `bug`     |
| Non-functional / technical / chore | `chore`   |

### Persona mapping

| Ticket type                | Persona in user story |
| -------------------------- | --------------------- |
| Functional / user-facing   | `user`                |
| Non-functional / technical | `developer`           |

### Project field IDs (project #6)

| Field        | ID          | Options                                                                                                               |
| ------------ | ----------- | --------------------------------------------------------------------------------------------------------------------- |
| Status       | `339282918` | Todo = `f75ad846`                                                                                                     |
| Priority     | `339282930` | Low = `97dc47ea` · Medium = `0d7428dc` · High = `c7f4ae7a`                                                            |
| Story Points | `339282931` | number                                                                                                                |
| Sprint       | `339282932` | Sprint 1 = `036f8164` · Sprint 2 = `5d1a1613` · Sprint 3 = `2a7d1ff3` · Sprint 4 = `2d2b8b72` · Sprint 5 = `91afdb67` |
| Component    | `339282935` | FE = `2a84a716` · BE = `249fc82b` · DevOps = `751b067c`                                                               |

### Sprint schedule

| Sprint   | Start      | End        |
| -------- | ---------- | ---------- |
| Sprint 1 | 2026-04-22 | 2026-05-12 |
| Sprint 2 | 2026-05-13 | 2026-05-26 |
| Sprint 3 | 2026-05-27 | 2026-06-09 |
| Sprint 4 | 2026-06-10 | 2026-06-23 |
| Sprint 5 | 2026-06-24 | 2026-07-07 |

Detect current sprint by today's date. Default to that sprint.

---

## Process — follow in order

### Step 1 — Parse input

Read the developer's freetext. Identify:

- How many tickets are described (one or multiple)
- Type of each: Functional · Non-Functional · Bug
- Any scope or context given

### Step 2 — Ask clarifying questions (ONE batch)

Before generating anything, identify ALL unclear points across ALL tickets and
ask them in a single message. Do not ask one at a time. Examples of things to
clarify:

- Who benefits and why (needed for user story)
- Vague scope ("improve performance" — of what, measured how?)
- Missing acceptance conditions
- Whether it's FE, BE, or both

Skip this step only if the description is completely unambiguous.

### Step 3 — Generate ticket drafts

For each ticket, generate:

**Title** — `Verb + Noun` format, concise, no prefix tag. Examples:

- `Initialize Next.js Project`
- `Configure ESLint and Prettier`
- `Add Responsive Navigation Bar`

**Body** — use this exact markdown structure:

```markdown
## Description

As a [user|developer], I want to [goal], so that [benefit].

[1–2 sentences of additional context if needed.]

---

## Figma Design

Figma link:
[FIGMA | NEOVAUDE WEBSITE](https://www.figma.com/design/fRzrn1dNzAi5rvpMdShvyK/NEOVAUDE-WEBSITE?node-id=0-1&t=mUIFO8DFJGUa9NQY-1)

<!-- Link to the specific frame/component if available. -->

---

## Acceptance Criteria

1. [Specific, testable condition]
2. [Specific, testable condition]
3. [Add more as needed]

---

## Technical Notes

- [Implementation hint or constraint]
- [Library, pattern, or file to use]
- [Edge case or gotcha to watch]

---

## References

- n/a
```

**Suggested fields:**

- **Priority** — High / Medium / Low with brief reason
- **Story Points** — Fibonacci (1, 2, 3, 5, 8, 13) with brief reason
- **Sprint** — current sprint by date, or next if clearly future work
- **Component** — infer from description if obvious, otherwise mark as "ask"
- **Label** — derived from type mapping above

### Step 4 — Show preview

Display the complete draft for every ticket. Format:

```
─────────────────────────────────────────
TICKET [n of N]
Title:         <title>
Label:         <label>
Priority:      <High|Medium|Low>  — <reason>
Story Points:  <n>  — <reason>
Sprint:        <Sprint N>  — <reason>
Component:     <value or "❓ ask">
─────────────────────────────────────────
## Description
...full body...
─────────────────────────────────────────
```

Then ask in one message:

1. Confirm or adjust Priority, Story Points, Sprint for each ticket
2. Confirm Component for each ticket (FE / BE / DevOps / FE&BE / BE&DevOps)
3. Any other changes before creating?

### Step 5 — Final confirmation

After receiving field confirmations, show a compact summary:

```
Ready to create N ticket(s):
  1. <title> — <label> · <priority> · <points>pt · <sprint> · <component>
  2. ...

Create? (yes / no)
```

Wait for explicit "yes" before proceeding.

### Step 6 — Execute

For each ticket, in sequence:

1. **Create the issue** via `mcp__github__issue_write` (method: `create`, owner:
   `Neovaude`, repo: `neovaude-website`)
2. **Add to project** via `mcp__github__projects_write` (method:
   `add_project_item`, owner: `Neovaude`, owner_type: `org`, project_number:
   `6`, item_type: `issue`)
3. **Set project fields** via `mcp__github__projects_write` (method:
   `update_project_item`) — one call per field:
   - Status → `f75ad846` (Todo)
   - Priority → option id from table above
   - Story Points → number
   - Sprint → iteration id from table above
   - Component → option id from table above

After all tickets are created, output:

```
✓ Created N ticket(s):
  • <title> → <GitHub issue URL>
  • ...
Added to project: Neovaude Website (#6)
```

---

## Rules

- Never skip the preview + confirmation steps
- Never create issues without explicit "yes"
- Never set Epic field
- Never assign to anyone
- Always include the Figma link in every ticket body
- Story points use Fibonacci only: 1, 2, 3, 5, 8, 13
- Titles must start with a verb (Initialize, Configure, Add, Build, Fix,
  Refactor, etc.)
- User story persona: `user` for functional, `developer` for
  non-functional/technical
