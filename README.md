<div align="center">

<img src="https://img.shields.io/badge/VS%20Code-v1.108%2B-007ACC?style=flat-square&logo=visualstudiocode&logoColor=white" />
<img src="https://img.shields.io/badge/GitHub%20Copilot-Agent%20Mode-6e40c9?style=flat-square&logo=github&logoColor=white" />
<img src="https://img.shields.io/badge/Claude%20Sonnet-4.6-D97706?style=flat-square" />
<img src="https://img.shields.io/badge/License-MIT-22c55e?style=flat-square" />
<img src="https://img.shields.io/badge/Language-EN%20%7C%20ES-64748b?style=flat-square" />

<br /><br />

# AgentForge

### *One prompt. Any repository. A complete, tailored GitHub Copilot agent setup — generated automatically.*

<br />

**AgentForge** is a reusable `.prompt.md` file you drop into any codebase.  
Run it once in VS Code agent mode and it explores your repo, understands your stack and domain,  
then generates the full Copilot configuration: custom agents, skills, instructions, and lifecycle hooks.

<br />

[**Get Started**](#-quick-start) · [**How It Works**](#-how-it-works) · [**What Gets Generated**](#-what-gets-generated) · [**Examples**](#-real-world-examples) · [**FAQ**](#-faq)

</div>

---

## The problem

You've started using GitHub Copilot agent mode. You're impressed. But it doesn't know:

- That `backend/` is read-only and should never be touched
- That your business uses terms like CVU, SKU, claim, or shipment
- That mutations go in Server Actions, not in components
- That your formatter is Biome, not Prettier
- That your test runner is `pnpm vitest run`, not `npm test`

So it guesses. It writes generic code. It touches files it shouldn't. You spend time correcting it.

**AgentForge fixes this.** It reads your repo and generates configuration that makes Copilot actually know your project.

---

## ✨ What makes this different

| Approach | Result |
|---|---|
| Generic Copilot instructions | Agent follows general best practices, ignores your conventions |
| Manual `.github/copilot-instructions.md` | Good, but static — you write it, you maintain it |
| **AgentForge** | Agent *explores* your repo, then generates everything from what it finds |

AgentForge produces **specific** output, not templates:

- If your API wrapper is called `apiRequest()`, the generated services use `apiRequest()`
- If your components folder is `src/ui/`, the instructions say `src/ui/`
- If your test runner is `cargo test`, the hooks run `cargo test`
- If your business is a PSP and you have `CVU`, `liquidation`, `chargeback` in your codebase, the domain skill defines them

---

## 🚀 Quick start

**Prerequisites:** VS Code with GitHub Copilot, agent mode enabled.

```bash
# 1. Copy the prompt to your repository
curl -o .github/prompts/forge.prompt.md \
  https://raw.githubusercontent.com/nipwd/AgentForge/main/forge.prompt_eng.md

# 2. Open VS Code in agent mode (Ctrl+Alt+I)
#    Select agent: Copilot (agent mode)
#    Click "Attach context" → "Prompt files" → forge.prompt.md
#    Or: Command Palette → "Chat: Run Prompt File"

# 3. The agent explores your repo and shows you a plan.
#    Confirm, and it generates everything.

# 4. Make hooks executable
chmod +x .github/hooks/*.sh

# 5. Enable in VS Code settings
```

```json
// .vscode/settings.json
{
  "chat.useAgentSkills": true,
  "chat.useCustomAgentHooks": true,
  "chat.agent.enabled": true
}
```

**That's it.** Open Copilot chat and you'll see your custom agents in the agent picker.

---

## ⚙️ How it works

AgentForge runs in **4 phases**:

```
Phase 1: Explore       Phase 2: Plan          Phase 3: Generate      Phase 4: Verify
─────────────────      ──────────────         ──────────────────     ───────────────
Read package.json  →   Show summary     →     Write all files   →    Cross-check
Read folder tree       List files to          Using real paths,       names, handoffs,
Read tsconfig/         generate               commands, domain        hooks, give
linting config         WAIT FOR USER          terms from repo         setup commands
Read README            CONFIRMATION
Infer domain
```

**Phase 2 is a deliberate pause.** Before writing a single file, the agent presents everything it found and asks you to confirm. You can correct anything before generation starts.

---

## 📦 What gets generated

Running AgentForge on a typical project produces:

```
.github/
├── copilot-instructions.md          ← Always-on global context
│
├── agents/
│   ├── [project]-dev.agent.md       ← Main development agent
│   ├── [project]-reviewer.agent.md  ← Pre-QA code review agent
│   └── [project]-tester.agent.md    ← Testing agent (if tests exist)
│
├── skills/
│   ├── [stack]-patterns/            ← Framework-specific code patterns
│   │   └── SKILL.md
│   ├── [domain]-domain/             ← Business domain glossary & types
│   │   └── SKILL.md
│   ├── [domain]-api-integration/    ← API integration patterns
│   │   └── SKILL.md
│   ├── [domain]-testing/            ← Test fixtures & mock patterns
│   │   └── SKILL.md
│   └── [domain]-code-review/        ← Review checklist & rules
│       └── SKILL.md
│
├── instructions/
│   ├── [module-a].instructions.md   ← Scoped rules (e.g.: components/**)
│   ├── [module-b].instructions.md   ← Scoped rules (e.g.: lib/actions/**)
│   └── [sensitive].instructions.md  ← Extra rules for critical modules
│
└── hooks/
    ├── block-readonly-dirs.sh       ← Blocks writes to vendor/, backend/, etc.
    ├── post-edit-format.sh          ← Auto-runs formatter + linter after edits
    └── run-tests-on-change.sh       ← Runs tests when test files are modified

copilot-setup.md                     ← Human-readable setup guide
```

### The agents

| Agent | Role | Has edit tools | Handoffs to |
|---|---|---|---|
| `[project]-dev` | Implement features end-to-end | ✅ | reviewer, tester |
| `[project]-reviewer` | Code review with project rules | ❌ | — |
| `[project]-tester` | Write and run tests | ✅ | reviewer |

No model is hardcoded in any agent. Each agent uses whatever model you have selected in the VS Code model picker at the time you start the chat. Switch models freely between sessions — your agents will always follow.

Agents are connected via **handoffs** — after the dev agent finishes, it shows a button that switches directly to the reviewer with context already loaded.

### The skills

Skills are loaded **on demand** into context, not all at once. Copilot reads the `description` of each skill and loads it only when relevant:

| Skill trigger example | Skill loaded |
|---|---|
| "create a new component for..." | `nextjs-patterns` or `[stack]-components` |
| "integrate the liquidations endpoint" | `api-integration` + `[domain]-domain` |
| "write tests for this service" | `[domain]-testing` |
| "review this before I push to QA" | `code-review` |

### The hooks

Hooks run automatically at agent lifecycle events — no manual invocation needed:

| Hook | Event | What it does |
|---|---|---|
| `block-readonly-dirs.sh` | **PreToolUse** | Blocks `exit 2` on any write to protected folders |
| `post-edit-format.sh` | **PostToolUse** | Runs formatter → linter → sends error summary to agent |
| `run-tests-on-change.sh` | **PostToolUse** | Runs test file after editing it; fails the session if tests break |

---

## 🌍 Real-world examples

### Example 1: Next.js + TypeScript PSP (fintech)
AgentForge detected: App Router, TypeScript strict, shadcn/ui, ESLint+Prettier, `backend/` (Laravel, read-only), domain terms: CVU, CBU, liquidation, compliance.

**Generated:**
- `psp-dev` agent that knows to never touch `backend/`, reads it as API contract docs
- `psp-domain` skill with full glossary (CVU, CBU, transaction states, AFIP retentions)
- `psp-api-integration` skill with `apiRequest()` wrapper patterns and Zod schemas
- `compliance.instructions.md` scoped to `app/**/compliance/**` with security rules
- Hook that blocks writes to `backend/` with `exit 2`

### Example 2: Rust + Axum API
AgentForge detected: Axum, Tokio, sqlx, no frontend, `cargo test` runner, domain: e-commerce order management.

**Generated:**
- `api-dev` agent with idiomatic Rust patterns (error handling with `thiserror`, `Result<T, AppError>`)
- `rust-patterns` skill with Axum handler templates, middleware patterns, sqlx query examples
- `ecommerce-domain` skill with Order, Shipment, SKU, Inventory types
- Hook running `cargo clippy --fix` and `cargo fmt` after edits

### Example 3: Python + FastAPI microservice
AgentForge detected: FastAPI, Pydantic v2, pytest, Ruff, no read-only dirs, domain: healthcare scheduling.

**Generated:**
- `scheduling-dev` agent with FastAPI router patterns and async SQLAlchemy
- `healthcare-domain` skill with Appointment, Practitioner, Slot, FHIR terminology
- `fastapi-patterns` skill with dependency injection templates, Pydantic schema patterns
- Hook running `ruff check --fix` and `ruff format` after edits

---

## 🗂 VS Code features used

AgentForge generates configuration that leverages the full agent customization stack shipped through April 2026:

| Feature | Since | Used for |
|---|---|---|
| Custom Agents (`.agent.md`) | v1.95 | The 3 specialized agents — model-agnostic |
| Agent Skills (`SKILL.md`) | v1.108 | Domain and stack knowledge |
| `.instructions.md` with `applyTo` | v1.95 | Scoped rules by file path |
| Agent-scoped Hooks (Preview) | v1.111 | Hooks inline in agent frontmatter |
| Monorepo customizations | v1.112 | Skills/agents discovered from root |
| Agent handoffs | v1.108 | Dev → Reviewer → Tester workflow |
| Autopilot mode | v1.111 | Works with all generated agents |
| `PreToolUse` / `PostToolUse` hooks | v1.109 | Format, lint, test, block writes |
| Chat Customizations editor | v1.112 | Unified UI to manage everything |

---

## 📁 Files in this repository

```
agentforge/
├── forge.prompt_eng.md   ← English version (use this one)
├── forge.prompt_esp.md   ← Spanish version (versión en español)
├── examples/
│   ├── nextjs-typescript/            ← Example output: Next.js App Router project
│   ├── rust-axum/                    ← Example output: Rust + Axum API
│   └── python-fastapi/              ← Example output: FastAPI microservice
└── README.md
```

---

## 🔧 FAQ

**Which model do the generated agents use?**  
None is hardcoded. All generated `.agent.md` files deliberately omit the `model:` field. This means every agent picks up whatever model you have selected in the VS Code model picker — Claude, GPT, Gemini, `auto`, or any BYOK model. You switch models from the picker and all your agents follow instantly, with no files to edit.

**Can I force a specific model for one agent?**  
Yes, manually add `model: claude-sonnet-4-6 (copilot)` (or any model name) to that agent's frontmatter after generation. The `model:` field also accepts an array to try models in order: `model: ['claude-opus-4-6 (copilot)', 'GPT-5.2 (copilot)']`.  
Yes. The prompt runs entirely inside your VS Code instance. Nothing leaves your machine.

**Can I run it on a repo that already has `.github/` config?**  
Yes. The agent will read existing files and incorporate them. If conflicts exist, it will ask before overwriting.

**What if my stack isn't Next.js?**  
AgentForge is stack-agnostic. It detects what you're using from the actual files in your repo. See the Rust and Python examples above.

**What if I don't have a `backend/` folder?**  
The `block-readonly-dirs.sh` hook is only generated if protected folders are detected. On repos with no read-only directories, it's omitted entirely.

**Do I need a paid Copilot plan?**  
Agent mode and custom agents require a Copilot paid plan (Individual, Business, or Enterprise). The free tier does not include agent mode.

**Can I run this multiple times?**  
Yes. Re-running on an evolved codebase will update the configuration to reflect changes. The Phase 2 pause lets you review before anything is overwritten.

**Is this affiliated with GitHub or Microsoft?**  
No. This is an independent open-source project. GitHub Copilot is a product of GitHub/Microsoft.

---

## 🤝 Contributing

Contributions welcome. Useful directions:

- **New example outputs** — run AgentForge on your stack and submit the result as an example
- **Hook improvements** — better error messages, more language support
- **Stack-specific skill templates** — if the generated skill for your stack is weak, improve the prompt rules
- **Translations** — the prompt currently exists in English and Spanish

Open an issue before starting significant work.

---

## 📄 License

MIT — do whatever you want with it.

---

<div align="center">

If AgentForge saved you time, consider starring the repo.  
It helps others find it.

⭐

</div>
