[README.md](https://github.com/user-attachments/files/30011333/README.md)
## Hi there 👋

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
# Chronos Lab

[![Chronos Lab](https://img.shields.io/badge/Chronos-Lab-c6f0ff?style=flat-square)](https://chronoslab.space)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](./LICENSE)

**The temporal compute layer for autonomous systems.**

Chronos gives agents the ability to branch, evaluate, and collapse millions of possible futures in milliseconds — so they can make decisions the way humans do: by thinking first.

🌐 **Production**: [chronoslab.space](https://chronoslab.space)

---

## What is Chronos?

Today's agents are reactive. They take an input, produce an output, and hope it's right. Chronos introduces **temporal computation** — computing over possible futures before acting.

A robot using Chronos can simulate:

- *What if I move my arm this way?*
- *What if the object slips?*
- *What if a human enters?*
- *What if the environment changes?*

Then it acts — with certainty. This is closer to how humans operate. We mentally simulate consequences. We imagine futures. We choose the best one.

## The stack

| Layer | Tech |
|---|---|
| Frontend | React 18, Vite, Tailwind CSS, TypeScript |
| Backend | Supabase (Postgres + RLS + Auth) |
| Domain | [chronoslab.space](https://chronoslab.space) |
| Deployment | Vercel / Netlify (single-page HTML bundle) |

## Quick start

```bash
# Clone
git clone https://github.com/ChronosLab-Space/chronos-lab.git
cd chronos-lab

# Install
npm install

# Configure
cp .env.example .env
# Fill in VITE_SUPABASE_URL and VITE_SUPABASE_ANON_KEY

# Run Supabase schema (run once in your Supabase SQL editor)
# See supabase/schema.sql

# Dev
npm run dev

# Build
npm run build
```

## Supabase setup

1. Create a project at [supabase.com](https://supabase.com)
2. Run `supabase/schema.sql` in the SQL editor
3. Copy the project URL and anon key to `.env`
4. Enable Row-Level Security on `access_requests` and `events` tables

The schema creates:

- `access_requests` — stores email submissions from the Request Access form
- `events` — lightweight analytics event log

Both tables are open for anonymous insert only. No client-side read/write.

`access_requests` also stores the required qualification context submitted by
each requester: email, name or organization, what they are building with AI
agents, and why temporal simulation matters for that workload.

## Project structure

```
src/
├── domain/          # Chronos entities, agents, language, simulations
├── application/     # fork / evaluate / collapse use cases
├── infrastructure/  # repositories/, auth/, storage/, queries/, Supabase bootstrap
├── presentation/    # React components, pages, router composition
├── main.tsx         # Browser bootstrap
└── index.css        # Tailwind + custom animations
supabase/
└── schema.sql       # Database schema + RLS policies
```

See [ARCHITECTURE.md](./ARCHITECTURE.md) for the layer responsibilities and
dependency rules.

See [PERFORMANCE.md](./PERFORMANCE.md) for virtualization, lazy-loading, and
simulation-cache rules.

### Repository adapters

Chronos persistence is defined by domain ports (`SimulationRepository`,
`AgentRepository`, `MemoryRepository`, `ScenarioRepository`) and can be backed
by interchangeable `MemoryRepository`, `SQLiteRepository`, or
`SupabaseRepository` implementations. See `ARCHITECTURE.md` for details.

## Routes

| Path | Description |
|---|---|
| `/` | Landing page |
| `/core` | Brief landing for the Core product |
| `/dashboard` | Full interactive dashboard |
| `/simulate` | Startup simulator demo |
| `/platform` | Architecture + primitives |
| `/docs` | Full documentation |
| `/developers` | SDK + CLI + API |
| `/about` | Company |
| `/contact` | X + Telegram + email |
| `/privacy` | Privacy policy |
| `/terms` | Terms of service |
| `/security` | Security information |

## The Chronos language

A domain-specific language for describing futures. Any agent can write Chronos:

```chronos
state {
  agent.runway = 12
  agent.mrr = 180
  context.board = "watching"
}

action "Raise Series A" {
  agent.runway = 24
  context.board = "hands-off"
  risk = 0.4
  reward = 0.82
}

score growth(state) {
  base = state.reward - state.risk
  if state.context.board == "watching" {
    base = base - 0.1
  }
  return clamp(base, 0, 1)
}

run {
  fork
  evaluate with growth
  collapse max-utility
}
```

## Target AI Service Topology

```text
Planner Agent → Scenario Generator → Branch Generator → Simulation Runtime
      → Outcome Evaluator → Ranking Engine → Memory
```

Each stage becomes an independently deployable service as the runtime scales.
See [ARCHITECTURE.md](./ARCHITECTURE.md) for service responsibilities,
repository boundaries, and dependency rules.

## Agent Operating System

```text
Planner → Task Graph → Scheduler → Execution Runtime
        → Memory → Evaluation → Timeline Ranking
```

Chronos executes tasks, not named agents. External providers register
capabilities for task kinds such as planning, scenario generation, simulation,
evaluation, ranking, and memory. This lets a capability be supplied by an LLM,
tool server, human approval workflow, or deterministic service without changing
the Temporal Engine.

### Automatic planning

For example, an objective like `Launch startup` becomes a task graph:

```text
Research competitors → Estimate market → Build roadmap
                     → Predict adoption → Financial simulation → Risk analysis
```

Chronos resolves registered capabilities for those tasks. The user never picks
an individual agent.

## Temporal Versioning

```text
Timeline → Branch → Subbranch → Merge → Collapse
```

Every decision creates replayable branches. Compatible branches can merge
evidence before ranking; collapse selects a canonical path while preserving the
discarded futures for audit and replay.

## Temporal Compute Platform

```text
SDK → API → CLI → Visual Studio Extension → Agent Runtime → Simulation Cloud
```

The same Chronos program can be authored in the editor, executed through the
CLI or SDK, submitted through the API, and observed or replayed in Simulation
Cloud. Every surface uses the same run, branch, timeline, and memory contract.

## Workspace Intelligence

```text
Workspace → Knowledge Graph → Past Simulations
          → Successful Futures → Failure Patterns → Next planning cycle
```

Chronos turns completed simulations into evidence. Validated futures become
reusable strategies; recurring failure patterns become constraints. This gives
the next decision cycle stronger priors without changing the deterministic
execution of an individual run.

The dashboard exposes this loop through the **Workspace Intelligence** tab,
where successful paths are promoted to evidence and recurring failure signals
become proposed constraints for future task planning.

## Contributing

This repo is a **public-facing marketing + docs site** for Chronos Lab. The core engine lives in a private repo.

Bug reports and suggestions welcome via GitHub Issues.

## Testing

Chronos treats simulation correctness as product correctness. The repository
includes unit tests for the engine, integration tests for language-to-simulation
flows, timeline snapshots, and Playwright end-to-end workflows.

```bash
npx vitest run
npx playwright install chromium
npx playwright test
```

See [TESTING.md](./TESTING.md) for the full testing strategy and coverage rules.

## License

MIT © 2026 Chronos Lab. See [LICENSE](./LICENSE).

## Links

- Website: [chronoslab.space](https://chronoslab.space)
- X: [@chronoslabspace](https://x.com/chronoslabspace)
- Telegram: [join group](https://t.me/+I9MN0GfvgwllZGRh)
- GitHub: [ChronosLab-Space](https://github.com/ChronosLab-Space)
