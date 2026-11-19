# skills

**AI skills for engineers who ship to production.**

AI can generate code all day. Production engineers are responsible for what happens when that code meets real users, real data, and real failure.

These skills build that responsibility into the agent’s workflow. They make it read before writing, trace before changing, test before claiming, and prove before shipping. Your codebase sets the rules. The agent does the work.

## Skills

| Skill                                  | What it does                                                                                                                                                                                                                                                                                                                                                 |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [review-pr](skills/review-pr/) | Reviews a PR, branch or set of commits like a senior engineer would. It states what changed and how behaviour changes against the requirement, then hunts correctness, security and convention issues. It runs the project's checks, reproduces findings, and gives you a report to paste straight to the developer: blockers, should-fix, nits, looks-good. |

## Install

| Agent                         | Command                                                                                        |
| ----------------------------- | ---------------------------------------------------------------------------------------------- |
| Claude Code                   | `/plugin marketplace add nowaliraza/skills` then `/plugin install review-pr@nowaliraza-skills` |
| GitHub Copilot CLI            | `/plugin marketplace add nowaliraza/skills` (reads `.claude-plugin/marketplace.json`)          |
| Codex CLI                     | `codex plugin marketplace add nowaliraza/skills`                                               |
| Gemini CLI                    | `gemini extensions install https://github.com/nowaliraza/skills`                               |
| Cursor, VS Code, Kiro, others | Agent Plugins 1.0 (`plugin.json`), or `npx skills add nowaliraza/skills`                       |

Or copy `skills/<name>/` into your agent's skills folder.

## Adding a skill

Create `skills/<name>/SKILL.md`, add a row to the table above, and add it to `.claude-plugin/marketplace.json`. The other manifests pick up everything in `skills/` automatically.
