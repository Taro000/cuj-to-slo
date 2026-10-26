# cuj-to-slo

English | [日本語](README.ja.md)

Agent skills that turn users, critical user journeys (CUJs) and requirements into quality-in-use requirements and KPIs, then functional requirements, then product and data quality requirements, and finally non-functional requirements with SLIs and SLOs. Every item traces back up the chain, so any SLO can be followed to the user it protects.

> **Status: experimental (0.2.0).** The chain has been run end to end once, on a fictional product with simulated answers. It has not been used on a real project yet, so check its first results closely.

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

Each skill drafts the whole stage first, then asks you about every point it was unsure of. Every judgment is marked `stated`, `inferred` or `confirmed`, so you can see what came from you and what the agent decided.

Each stage is a long task. For a mid-sized product, expect tens of items per stage, and plan time to review them and to agree on the priorities and targets; that agreement is the point of the exercise.

Questions and artifacts follow your language and your project's documentation language. The skills themselves are written in English.

## Artifacts

```
docs/quality/
├── 1-quality-in-use.md … 4-nfr-slo.md   one report per stage
├── context.yaml                         system, KGIs, outcomes, users, CUJs, hints for later stages
├── qiu/  kpi/  fr/  frp/  qr/           one YAML file per requirement
└── nfr/  sli/  ebp/
```

YAML keys and IDs are fixed in English, so every stage can read every other stage's files.

## What the skills will not do

- Hand targets down from the top. SLO targets start provisional, from stated numbers or baselines, and are marked as needing agreement.
- Invent numbers. A threshold or target that your inputs do not give stays a named placeholder until stage 4 proposes a provisional value, with what it is based on.
- Fill in what a law or standard requires from general knowledge. When you name one without its content, the skills ask.
- Decide business priorities for you. Priorities, targets, exclusions and error budget policies are drafted and marked "needs agreement".
- Write requirements for business procedures, operations or training. When only those can meet a need, the report says so in one line.

## Maintaining

`references/conventions.md`, `references/artifacts.md` and `references/glossary.md` are identical in every skill, because `npx skills add --skill` installs a single skill directory. Edit them in `shared/`, then run:

```sh
scripts/sync-shared.sh          # copy shared/ into every skill
scripts/sync-shared.sh --check  # fail if any copy differs
```

## License

MIT
