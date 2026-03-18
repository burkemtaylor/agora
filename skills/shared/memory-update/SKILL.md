# Memory Update

Write a specific learning or decision to the project's persistent memory.

## Usage
```
/memory-update
```

Use this when you want to capture something specific mid-conversation — a decision made, a constraint discovered, a preference established — without running a full retrospective.

## Steps

1. **Identify what to capture** — Ask the user: "What do you want to remember?" if not already stated. Clarify whether this is a:
   - **feedback** memory: a process/approach rule (e.g. "don't mock the DB in tests")
   - **project** memory: context or a decision about ongoing work
   - **user** memory: something about the user's role, preferences, or knowledge
   - **reference** memory: a pointer to an external resource

2. **Draft the memory** — Write it in the standard format. Lead with the rule or fact, then **Why:** and **How to apply:** lines for feedback/project types.

3. **Check for duplicates** — Read the project's `MEMORY.md` index. If a relevant memory already exists, update it rather than creating a duplicate.

4. **Write the file** — Save to `memory/{type}_{name}.md` in the current project directory.

5. **Update the index** — Add or update the entry in `memory/MEMORY.md`.

6. **Confirm** — Tell the user what was saved and where.
