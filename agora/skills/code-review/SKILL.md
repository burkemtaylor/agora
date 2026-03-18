---
name: code-review
description: "Review code for correctness, clarity, security, and maintainability"
argument-hint: "[optional: file path, diff, or description of what to review]"
---

# Code Review

Review code for correctness, clarity, security, and maintainability.

## Usage
```
/code-review [target]
```

**target** (optional): a file path, diff, or description of what to review. If omitted, reviews the current working changes (`git diff`).

## Steps

1. **Get the code** — Read the target file(s) or run `git diff` / `git diff HEAD` to get the current changes. Ask the user for context if the scope is unclear.

2. **Understand intent** — Before critiquing, understand what the code is trying to do. Read related files if needed for context.

3. **Review across dimensions:**
   - **Correctness** — Does it do what it claims? Edge cases? Off-by-ones? Error handling?
   - **Clarity** — Is it easy to understand? Are names meaningful? Is complexity justified?
   - **Security** — Injection risks, untrusted input, exposed secrets, auth gaps?
   - **Performance** — Any obvious inefficiencies, N+1s, unnecessary allocations?
   - **Maintainability** — Is it over-engineered? Under-abstracted? Will this be painful to change?
   - **Tests** — Are there tests? Do they cover the important cases?

4. **Prioritize feedback** — Separate must-fix issues from suggestions. Don't bury critical issues in a list of nits.

5. **Be specific** — Reference file paths and line numbers. Explain *why* something is a problem, not just that it is.

6. **Acknowledge what's good** — Note approaches that are well done, especially non-obvious good choices.
