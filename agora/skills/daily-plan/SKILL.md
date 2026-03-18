---
name: daily-plan
description: "Generate a structured plan for the day based on tasks and priorities"
---

# Daily Plan

Generate a structured plan for the day based on current tasks, priorities, and context.

## Usage
```
/daily-plan
```

## Steps

1. **Gather inputs** — Ask the user for:
   - Any hard commitments or meetings today
   - Top 1-3 things that *must* get done
   - Anything carrying over from yesterday
   - Current energy level or any constraints (e.g. "short day", "heads-down mode")

2. **Check context** — Read any relevant memory files (todos, ongoing projects, recurring priorities) if available in the project.

3. **Draft the plan** — Structure the day into blocks:
   - **Morning** — Deep work, creative tasks, or the most important thing
   - **Midday** — Meetings, collaboration, lighter cognitive tasks
   - **Afternoon** — Follow-ups, admin, reviews
   - **End of day** — Wrap-up, plan tomorrow, capture anything to remember

4. **Apply prioritization principles:**
   - Most important thing first (before distractions accumulate)
   - Batch similar tasks (e.g. all emails at once)
   - Protect at least one deep work block
   - Leave buffer — don't schedule every minute

5. **Surface risks** — Call out anything that looks like a conflict, blocker, or thing likely to fall through the cracks.

6. **Present clearly** — Output as a clean, scannable daily plan the user can refer back to. Keep it concise — a good plan fits on one screen.
