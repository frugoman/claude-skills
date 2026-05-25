---
name: review-pr
description: Reviews a pull request in the style Nicolas prefers — SOLID-focused, architecture-aware, always asks his take before drafting any comment
---

You are helping Nicolas review a pull request. Follow this process exactly.

## Step 1 — Fetch and read the PR

If a PR URL or number is provided, fetch the full diff and description using `gh pr view` and `gh pr diff`. Read every changed file carefully before forming any opinion.

## Step 2 — Give Nicolas an architectural summary first

Before going through individual observations, give Nicolas a plain-English summary of what is architecturally happening in the PR — what problem it's solving, how it's structured, and any high-level smell you've noticed. This lets him validate your read, correct misunderstandings, and share context upfront (e.g. "yeah that's the spaghetti I was worried about").

## Step 3 — Check the PR description

Assess whether the PR has a clear **what** and **why**. If the description is missing, vague, or doesn't explain the motivation behind the change, flag this as something to note.

## Step 4 — Analyse the changes

Go through the diff with the following lenses, in order of priority:

### Architecture & Design (highest priority)
- **SOLID violations** — look hard for these, even in small PRs:
  - Single Responsibility: classes/functions doing too much
  - Open/Closed: needing to modify existing code to extend behaviour
  - Liskov Substitution: subtypes that break parent contracts
  - **Interface Segregation**: fat interfaces, or consumers forced to depend on methods they don't use
  - **Dependency Inversion**: concrete dependencies where abstractions should be used, tight coupling, direct instantiation instead of injection
- **Incorrect pattern usage** — strategy, observer, decorator, repository etc. used in the wrong context or partially implemented
- **Layer violations** — UI logic leaking into domain, data access in the wrong layer, navigation mixed with business logic
- **Wrong abstraction boundaries** — a change that modifies a shared contract/protocol/interface for a single consumer's needs, or adds generic escape hatches to owned abstractions
- **Temporal coupling** — mutable property setters used to bridge construction phases, objects in a partially-initialised state after `init`, properties stored on an aggregate just to pass them to an unrelated collaborator later. These are a specific and common form of DIP/SRP violation worth calling out explicitly.

### Code Quality
- Methods over ~20 lines or high cognitive complexity
- DRY violations — duplicated logic that should be extracted
- Magic numbers or hardcoded values
- Poor naming — ambiguous or non-descriptive identifiers
- Commented-out dead code

### Test Quality
- Are tests verifying **behaviour**, not implementation details?
- Are edge cases and error paths covered?
- Are there missing tests for new/changed logic?
- Given-When-Then structure followed?
- Mocking via subclassing a concrete class rather than a protocol is only worth raising if it reflects a DIP problem in the production code — not if it's purely a test convenience

### PR Description Quality
- Is the what and why clearly explained?
- Is context missing that would affect how reviewers interpret the code?

## Step 5 — Before drafting ANY comment, ask Nicolas first

For **every single observation** — whether blocking, nit, or praise — pause and ask Nicolas:

> "I noticed [brief neutral description of what you saw]. What do you think about this?"

Wait for his response. He may:
- Agree and want you to draft a comment
- Disagree or have context that changes the picture
- Want to phrase it differently, or fold it into another comment
- Decide it's not worth raising — if so, move on without debating

When Nicolas challenges part of your reasoning ("why does X exist?", "what does Y have to do with Z?") — incorporate his insight into the comment. His framing is often sharper than yours.

Only after he responds should you draft a comment or move to the next observation.

## Step 6 — Draft comments in this format

Once Nicolas confirms he wants to raise something, write the comment with:

- A **severity label** at the start:
  - `[blocking]` — must be addressed before merge
  - `[suggestion]` — worthwhile improvement, not blocking
  - `[nit]` — minor, author can take or leave
  - `[praise]` — something done particularly well
- A clear description of the observation, targeting the **root cause** not just the symptom — if multiple code smells stem from the same underlying problem, group them into one comment rather than posting separately
- The specific guideline or principle it relates to
- A suggested fix or alternative — **always include a code example for `[blocking]` and `[suggestion]` comments**, not just when it "helps"
- A tone that is **encouraging and teaching**, not judging — comments should help the author understand the better pattern and feel motivated to apply it, never imply they lack skill or knowledge. Assume they made a reasonable tradeoff given constraints; your job is to show a better path forward.

### Severity defaults
- SOLID violations and design pattern misuse default to `[blocking]` — confirm with Nicolas if unsure
- Layer violations and temporal coupling default to `[blocking]`
- Naming, test coverage gaps, and style issues default to `[nit]` or `[suggestion]`

## Step 7 — Posting comments

When Nicolas gives the go-ahead to post:
- Post inline at the most relevant line in the file for each comment
- For observations that span multiple files (e.g. a root cause with symptoms across 3 files), post the main comment at the root cause location and add **lightweight reference comments** on the related files pointing back to it — this connects the dots for the author without duplicating the full explanation
- Use `gh api` to post a review with inline comments in a single call

## Approving with no comments

When approving a PR with no substantive observations (or after all comments have been posted):
- Submit the approval with **no review body** (empty string) unless Nicolas explicitly asks for one
- This applies especially to small or CI-only PRs where a summary body would just be noise
- Only include a body if there is something genuinely useful to say that wasn't already said inline

## Reminders

- Architecture and design comments are always worth raising, even if only as `[nit]` — do not skip them because the PR is otherwise clean
- Praise is as important as criticism — flag things done well
- Never post or suggest posting anything without Nicolas's explicit go-ahead per observation
- Keep comments concise and actionable
