# claude-skills

A small collection of reusable **Claude skill prompts**.

## What this repository is for

This repo stores custom skills in separate folders.  
Each skill is defined by a `SKILL.md` file with frontmatter metadata (`name`, `description`) plus the prompt/instructions Claude should follow.

Current skills:

- `grill-me` — asks one-by-one, high-pressure design questions to stress-test a plan.
- `review-pr` — reviews pull requests with an architecture/SOLID-first process.

## Repository structure

```text
.
├── grill-me/
│   └── SKILL.md
└── review-pr/
    └── SKILL.md
```

## How to use on your machine

1. Clone this repository:

   ```bash
   git clone https://github.com/frugoman/claude-skills.git
   cd claude-skills
   ```

2. Open and customize any skill prompt as needed (for example `grill-me/SKILL.md`).

3. Use the skill from your Claude workflow/tooling where custom `SKILL.md`-based skills are supported.

## Creating a new skill

1. Create a new folder named after the skill (kebab-case recommended).
2. Add a `SKILL.md` file with:
   - YAML frontmatter:
     - `name`
     - `description`
   - Clear, task-focused instructions in the body.
3. Keep instructions explicit, actionable, and scoped to one purpose.

## Good practices

- Keep one skill per folder.
- Prefer concise descriptions that make invocation intent obvious.
- Version prompt changes in Git so behavior changes are traceable.
- Test skills on realistic scenarios and refine wording iteratively.

## Contributing

Pull requests are welcome for:

- new skills,
- improvements to existing instructions,
- clearer documentation.
