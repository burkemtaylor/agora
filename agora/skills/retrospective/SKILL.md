---
name: retrospective
description: "Run a structured retrospective to capture learnings to memory"
argument-hint: "[optional: scope — e.g. 'last sprint', 'this feature']"
---

# Retrospective

Run a structured retrospective to capture learnings and write them to memory for compound improvement over time.

## Usage
```
/retrospective [scope]
```

**scope** (optional): what to retrospect on — e.g. "last sprint", "this feature", "this week", "the auth refactor". Defaults to the current project/conversation context.

## Steps

1. **Gather context** — Read recent git history, task completions, and any existing memory files relevant to the scope. Ask the user for any additional context not visible in files.

2. **What went well** — Identify decisions, approaches, or habits that produced good outcomes. Be specific: not "communication was good" but "daily standups caught the API contract mismatch early."

3. **What didn't go well** — Identify friction, mistakes, or missed opportunities. Focus on patterns, not blame.

4. **What we'd do differently** — Concrete, actionable changes for next time. Each item should be specific enough to act on.

5. **Key decisions made** — Capture any significant decisions and *why* they were made. These are often lost otherwise.

6. **Write to memory** — For each significant learning, write a memory file in the project's `memory/` directory using the standard frontmatter format:
   - `feedback_*.md` for process/approach learnings
   - `project_*.md` for project-specific context and decisions
   - Update `MEMORY.md` index with any new files

7. **Summary** — Present a concise summary of what was captured and where it was saved.

## Memory Format
```markdown
---
name: <short_name>
description: <one-line description of the learning>
type: feedback | project
---

<The learning itself — lead with the rule or fact>

**Why:** <what caused this learning or decision>
**How to apply:** <when this should influence future behavior>
```
