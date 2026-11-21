# AGENTS.md

How agents work in this repository. What the system is lives in [ARCHITECTURE.md](ARCHITECTURE.md); security rules live in [SECURITY.md](SECURITY.md). Read both before any change.

## Guardrails
- If a task conflicts with ARCHITECTURE.md or SECURITY.md, stop and raise the conflict. Do not silently deviate.
- Treat `UNKNOWN`, `TO BE DECIDED`, and `{{PLACEHOLDER}}` values as open decisions. Do not resolve them by assumption. Ask, or record the question in the issue.
- Do not add frameworks, services, or infrastructure that ARCHITECTURE.md does not list.
- Keep changes scoped to the requirement being worked on. Do not refactor unrelated code.
- If a change alters the architecture or a security decision, update ARCHITECTURE.md or SECURITY.md in the same change. Do not restate security rules elsewhere, including code comments.

## Issues and Requirements
- New GitHub issues MUST use [.github/ISSUE_TEMPLATE/requirement.yml](.github/ISSUE_TEMPLATE/requirement.yml).
- Implement against the issue's RFC 2119 statements and acceptance criteria (AC-01..AC-03 at minimum), and meet the template's coverage requirement.
- Reference the `REQ-[MODULE]-[###]` ID in commits and PRs.

## Code Complexity
- Per function: cyclomatic complexity below 7, cognitive complexity at most 10, at most 2 levels of nesting.
- Use guard clauses instead of nested if/else. Extract complex conditions and inner loops into named helpers.

## Tooling (undecided)
- Python and JS/TS linters and complexity checkers: TO BE DECIDED
- Formatters: TO BE DECIDED
- Test runners (Python, JS): TO BE DECIDED
- Local setup and run commands: UNKNOWN
- Branching, PR review rules, and commit message convention: TO BE DECIDED
- CI/CD pipeline: TO BE DECIDED (tracked as `{{CI_CD_SYSTEM}}` in SECURITY.md)

## Finishing a Task
- Run the linter to verify the complexity limits. If no linter is configured yet, say so; do not claim the limits were verified.
- Report what changed and which checks ran (lint, tests) with their results.
- List checks that were skipped or could not run, and any open UNKNOWNs you hit.
