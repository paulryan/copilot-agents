# copilot-agents

A set of GitHub Copilot custom agents that implement a TDD-driven development workflow with automated code review.

## Agents

All agent definitions live in [`.github/agents/`](.github/agents/).

### Orchestrator

**Entry point for feature work.** Breaks a task into small, independently implementable components, delegates each to the Developer agent, then sends the result to the Code Reviewer for feedback. Routes Critical/Warning findings back to the Developer for fixes (up to 3 cycles per component) and escalates unresolved issues to the user.

- Model: Claude Opus 4.6
- Tools: agent, read, search
- Delegates to: Developer, Code Reviewer

### Developer

Implements code using a strict **Red → Green → Refactor → Document** TDD workflow. Before writing any implementation, delegates test creation to the Unit Tester agent. Aims for high, meaningful test coverage without gaming the metric.

- Model: Claude Opus 4.6
- Tools: agent, read, search, edit, execute, vscode
- Delegates to: Unit Tester

### Unit Tester

Testing specialist that writes tests **before** implementation exists. Auto-detects the repository's test framework, follows Arrange-Act-Assert structure, and covers happy paths, edge cases, and error cases. For existing behavior validation or refactors, tests may pass when first added.

- Model: Claude Opus 4.6
- Tools: read, search, edit, execute

### Code Reviewer

Read-only reviewer on a **separate model** for independent perspective. Provides severity-ranked findings (Critical / Warning / Nit) focused on correctness, security, maintainability, and performance. Does not make code changes.

- Model: GPT-5.3-Codex
- Tools: read, search

## Workflow

```
User
 └─► Orchestrator
      ├─► Developer ◄──► Unit Tester  (TDD loop)
      ├─► Code Reviewer                (review)
      └─► Developer                    (fix Critical/Warning findings)
           ... repeat up to 3 cycles per component ...
```

1. **Plan** — Orchestrator decomposes the task and presents the build order for approval
2. **Implement** — Developer builds each component using TDD (Unit Tester writes tests first)
3. **Review** — Code Reviewer provides severity-ranked feedback
4. **Fix** — Developer addresses Critical and Warning findings; Orchestrator re-reviews
5. **Deliver** — Orchestrator reports what was built, test coverage, deferred nits, and remaining concerns

## Setup

Copy the `.github/agents/` directory into your repository. The agents are automatically available in VS Code via GitHub Copilot Chat when using the `@` agent picker.

## Design Decisions

- **Mixed models** — The Code Reviewer intentionally uses a different model than the other agents to provide an independent review perspective.
- **Minimal tool scope** — Each agent only has the tools it needs. The Orchestrator and Reviewer are read-only; only the Developer and Unit Tester can edit files.
- **Preference-based coverage** — Coverage targets are a preference, not a hard gate. The goal is meaningful tests on critical paths, not metric gaming.
- **Iteration limits** — Each component is limited to 3 Developer/Reviewer cycles to prevent infinite loops; unresolved issues escalate to the user.
