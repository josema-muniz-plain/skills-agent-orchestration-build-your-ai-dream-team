# Agent team

I am using GitHub Copilot CLI in a Codespace to orchestrate this work. The custom agent team below builds Mona's Project Pulse dashboard. All definitions live in `.github/agents/`.

| Agent | Target model | Responsibility | Definition |
|-------|--------------|----------------|------------|
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks down the request, delegates to specialists with explicit file scopes, runs work in parallel only when scopes don't overlap, verifies the integrated result. Does not implement or run git operations. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repo and docs, then returns an implementation plan with steps, file assignments, dependencies, edge cases and validation. Writes no code. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements code and logic within its assigned files, including runnable app support such as `.vscode/launch.json`, and validates its changes. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Handles UI/UX, accessibility, and styling: a polished, responsive dashboard with project cards, status badges and clear priority treatment. | `.github/agents/designer.agent.md` |

The learner controls all git operations (stage, commit, push) through Copilot CLI prompts.
