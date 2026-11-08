<p align="center">
  <img src=".github/assets/logo.svg" alt="cuj-to-slo logo" width="640">
</p>

# cuj-to-slo

English | [日本語](README.ja.md)

Agent skills that turn users, critical user journeys (CUJs) and requirements into quality-in-use requirements and KPIs, then functional requirements, then product and data quality requirements, and finally non-functional requirements with SLIs and SLOs. Every item traces back up the chain, so any SLO can be followed to the user it protects.

> **Status: experimental (0.3.0).** The chain has been run end to end on a fictional product with simulated answers and on one real demo project. Version 0.3.0 changes how the skills ask questions, record assumptions and keep history, and keeps design decisions out of the requirements; it has not been run end to end yet, so check its first results closely.

## The chain

| Stage | Skill | Input | Output |
|---|---|---|---|
| 1 | `cuj-to-quality-in-use` | Users, CUJs and requirements you provide | Quality-in-use requirements, means KPIs |
| 2 | `quality-in-use-to-functional-requirements` | Stage 1 output | Functional requirements with acceptance criteria |
| 3 | `functional-requirements-to-product-quality` | Stage 2 output, plus requirements that do not come from users (laws, standards, security risk analysis, development policies) | Prioritized product and data quality requirements |
| 4 | `product-quality-to-slo` | Stage 3 output, plus operational facts (telemetry, traffic, baselines) | Non-functional requirements, SLI definitions, provisional SLOs, error budget policy, alerts (burn-rate, or progress alerts for deadlines) |

Stages 3 and 4 never add functional requirements themselves. When a quality requirement needs a function that is missing, they write a function proposal; re-run stage 2 to accept or reject it.

## Install

```sh
# all four skills, into the current project
npx skills add Taro000/cuj-to-slo

# list the skills without installing
npx skills add Taro000/cuj-to-slo --list

# one skill, for Claude Code only, available in every project
npx skills add Taro000/cuj-to-slo --skill product-quality-to-slo -a claude-code -g
```

## Use

Run the stages in order, each in a fresh request:

1. "Here are our users, CUJs and business goals: … Derive quality-in-use requirements and KPIs."
2. "Turn the quality-in-use requirements into functional requirements."
3. "Derive product and data quality requirements for these functions. Legal requirements: …"
4. "Turn the quality requirements into NFRs and SLOs. We have load balancer logs, about 300,000 checkouts a day."
5. If stage 3 or 4 raised function proposals: "Re-run stage 2 to resolve the function proposals." Then re-run stages 3 and 4 for the new functions.

Each skill drafts the whole stage first. It then rates every point it was unsure of by breadth (how much depends on it) and severity (the harm if the draft is wrong), and asks you only about the points where either is high. Priorities, targets and policies are rated the same way; when they are asked, your answer is the decision. Two decisions are always asked, whatever their ratings: which quality requirements each CUJ emphasizes, and the error budget policy. Every other point is kept as drafted and recorded as an assumption, which the skill lists at the end for you to skim and correct if you want. Every judgment is marked `stated`, `inferred`, `assumed` or `confirmed`, so you can see what came from you, what the agent decided, and what nobody has checked yet.

Each stage is a long task. For a mid-sized product, expect tens of items per stage, and plan time to review them and to answer the questions about priorities and targets; those answers are the point of the exercise.

The chain says what the system must do and how well, never how. It does not choose platforms, products, the names or protocols of interfaces, data stores, mechanisms or measurement tools, and it does not ask you about them. When your inputs contain such decisions, it keeps them in `context.yaml` for the design (for example, the companion skills in [req-to-design](https://github.com/Taro000/req-to-design)) and leaves them out of the requirements, so that a design change does not mean rewriting them.

Reports and items show only the current state; git keeps the history. Each skill ends its run by committing the files it wrote, with the reasons in the commit message, and the next run reads what changed since then.

Questions and artifacts follow your language and your project's documentation language. The skills themselves are written in English.

## Artifacts

```
docs/quality/
├── 1-quality-in-use.md … 4-nfr-slo.md   one report per stage
├── context.yaml                         system, shared facts, design inputs, KGIs, outcomes, users, CUJs, hints
├── qiu/  kpi/  fr/  frp/  qr/           one YAML file per requirement
├── nfr/  sli/  ebp/
└── qas/                                 one YAML file per assumption made without asking
```

YAML keys and IDs are fixed in English, so every stage can read every other stage's files.

## What the skills will not do

- Hand targets down from the top. SLO targets start provisional, from stated numbers or baselines; the ones that matter most are asked, and the rest are recorded as provisional decisions.
- Invent numbers. A threshold or target that your inputs do not give stays a named placeholder until stage 4 proposes a provisional value, with what it is based on.
- Fill in what a law or standard requires from general knowledge. When you name one without its content, the skills ask.
- Decide business priorities for you. Priorities, targets, exclusions and error budget policies are business decisions: each CUJ's emphasized requirements and the error budget policy are always asked, the others when their breadth or severity is high, and the rest are adopted provisionally and recorded as assumptions, never presented as decided.
- Decide the design. Platforms, products, the names and protocols of interfaces, data stores, mechanisms and measurement tools are left to the design.
- Write requirements for business procedures, operations or training. When only those can meet a need, the report says so in one line.

## Maintaining

`references/conventions.md`, `references/artifacts.md` and `references/glossary.md` are identical in every skill, because `npx skills add --skill` installs a single skill directory. Edit them in `shared/`, then run:

```sh
scripts/sync-shared.sh          # copy shared/ into every skill
scripts/sync-shared.sh --check  # fail if any copy differs
```

## License

MIT
