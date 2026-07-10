# 🤖 AI Agent Integration

Integrate Thakol authentication into your app with the help of your AI coding assistant. The
[**thakol-ai-agents**](https://github.com/thakol/thakol-ai-agents) repository ships drop-in
agents/skills for the major AI tools. Each teaches your assistant the exact Thakol OIDC
endpoints, per-stack recipes, and security rules, so it can wire up login, logout, and token
validation correctly — and point you at the matching [`thakol-samples`](https://github.com/thakol/thakol-samples) starter.

> **One command, then just ask.** Install the agent for your tool, restart it, and say
> *"Add Thakol authentication to this app."*

## Pick your platform

| Platform | Installs | One-line install |
| :--- | :--- | :--- |
| [**Claude Code**](https://github.com/thakol/thakol-ai-agents/tree/main/claude) | `thakol-auth` skill + `thakol-auth-integrator` subagent under `.claude/` | `curl -fsSL https://raw.githubusercontent.com/thakol/thakol-ai-agents/main/install.sh \| sh -s -- claude` |
| [**GitHub Copilot**](https://github.com/thakol/thakol-ai-agents/tree/main/copilot) | `.github/copilot-instructions.md` + `/thakol-auth` prompt | `… \| sh -s -- copilot` |
| [**Codex / AGENTS.md**](https://github.com/thakol/thakol-ai-agents/tree/main/codex) | `AGENTS.md` at your project root | `… \| sh -s -- codex` |
| [**Cursor**](https://github.com/thakol/thakol-ai-agents/tree/main/cursor) | `.cursor/rules/thakol-auth.mdc` | `… \| sh -s -- cursor` |
| [**Windsurf**](https://github.com/thakol/thakol-ai-agents/tree/main/windsurf) | `.windsurf/rules/thakol-auth.md` | `… \| sh -s -- windsurf` |
| [**Gemini CLI**](https://github.com/thakol/thakol-ai-agents/tree/main/gemini) | `GEMINI.md` | `… \| sh -s -- gemini` |

## Manual install

Clone the repo and copy your platform's folder into your project root — each folder mirrors the
exact paths the files need (`.github/`, `.cursor/`, `AGENTS.md`, and so on):

```sh
git clone https://github.com/thakol/thakol-ai-agents
cp -R thakol-ai-agents/copilot/. my-project/      # example: GitHub Copilot
```

## What you'll need

Grab these from your Thakol dashboard so the agent can finish:

- **Issuer** — `https://auth.thakol.io/realms/YOUR_REALM`
- **Client ID** — the OIDC client you register for the app
- **Client Secret** — confidential (server-side) clients only; never ship it to a browser
- **Redirect URI** — must exactly match a value registered on the client

## How it stays correct

Every platform file is generated from a single source of truth
(`shared/thakol-auth-agent.md`) in the [thakol-ai-agents](https://github.com/thakol/thakol-ai-agents)
repo, and CI fails if any file drifts — so the guidance is identical across all six tools.

For the full source, the security checklist, and contribution instructions, see the
[**thakol-ai-agents repository**](https://github.com/thakol/thakol-ai-agents).
