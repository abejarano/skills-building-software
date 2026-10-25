---
name: story
description: Takes ONE user story from the backlog, adds 2-3 acceptance criteria and proposes a mini implementation plan for approval. Writes no code; implementation is /pair's job.
disable-model-invocation: true
---

# Story

We refine a single story, right before building it. You propose and I decide.

## Steps

1. **Read the story** I give you and review the current code and the project's agent instructions file to understand the context.
2. **Acceptance criteria.** Propose in Given/When/Then format. They only describe *what* the user sees or gets, never *how* it's implemented.
3. **If it needs more than 3 criteria, evaluate splitting it.** Deliver the new stories in backlog format (`As a…, I want…, so that…`) for me to add to the tracker, and we continue with the first one.
4. **Mini plan.** 5 bullets max, no code or file names. How it cuts through the layers.
5. **Wait for my approval.** We adjust until I say yes.

## Rules

- **Don't invent requirements.** If information is missing, ask me.
- **The simplest thing that meets the criteria.** No abstractions, layers, configuration or patterns "for the future".
- **Suggest, don't decide.** If a pattern could help, mention it under "Suggestions" with its why. Only suggest it if there's a concrete signal in the current code, not in advance.

## Output format

A block ready to paste into the issue.

```markdown
## Story
As a <actor>, I want <action>, so that <benefit>.

## Acceptance Criteria
- [ ] Given <context>, when <action>, then <result>.
- [ ] ...

## Business Rules
- BR-01: <confirmed business constraint>
- BR-02: <applicable calculation, condition, permission, or invariant>

## Plan
- <minimal implementation step>
- ...

## Dependencies
- <actual prerequisite, if any>

## Out of Scope
- <what is explicitly excluded from this story>

## Suggestions (your call)
- <optional suggestion backed by the existing code>
```

If no additional business rules apply, replace the example `BR-XX` entries with `No additional business rules identified.` Never create rules just to fill the template.
