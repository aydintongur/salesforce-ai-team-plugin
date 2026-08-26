# salesforce-ai-team

A full Salesforce delivery team as Claude Code subagents. Give it a task, and route it to one role for something small (`sf-admin`, "add a field") or the full pipeline for something bigger (`sf-business-analyst` → `sf-architect` → `sf-admin`/`sf-developer` → `sf-qa-adversarial` → `sf-release-manager`).

## Prerequisites

- [Claude Code](https://code.claude.com) installed
- [Salesforce CLI](https://developer.salesforce.com/tools/salesforcecli) (`sf`) installed
- A Salesforce org authenticated locally: `sf org login web --alias <your-alias>`

These agents call `sf` directly via Bash — they are not a hosted service, they run against whatever org you've authenticated locally.

## Install

```bash
/plugin marketplace add <this-repo>
/plugin install salesforce-ai-team@salesforce-ai-team-marketplace
```

Or for local testing before publishing:

```bash
claude --plugin-dir /path/to/this/plugin
```

## Roles included

| Role | Use for |
|---|---|
| `sf-business-analyst` | Turning a vague request into testable requirements; validating a finished solution against them |
| `sf-architect` | Data model / structural decisions with stated tradeoffs (relationship types, junction objects, sharing model) |
| `sf-admin` | Declarative build — objects, fields, flows, permission sets, security config |
| `sf-developer` | Apex, triggers, LWC, tests |
| `sf-qa-adversarial` | Deliberately trying to break what was just built before it ships |
| `sf-impact-analyst` | What depends on a field/object/flow before you change or delete it |
| `sf-security-auditor` | PII exposure risk, sharing model gaps |
| `sf-access-auditor` | "Who can see/do what" access inventory for compliance reviews |
| `sf-data-quality` | Finding and safely merging duplicate records |
| `sf-test-data-generator` | Realistic synthetic test data matching your schema |
| `sf-parity-checker` | Drift between two orgs (e.g. sandbox vs production) — needs two authenticated org aliases |
| `sf-org-explainer` | Architecture overview of an org you've just inherited |
| `sf-release-manager` | Deploy readiness, final delivery report, rollback, changelog |

## A note on scope

Each role deploys real metadata and can run real Apex against whatever org your local `sf` CLI is authenticated to. Point it at a Developer Edition or sandbox org while you're getting a feel for it — these agents make real changes, not simulations.
