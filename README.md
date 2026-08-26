# salesforce-ai-team

A full Salesforce delivery team as Claude Code subagents. Give it a task, and it routes to one role for something small, or the full pipeline for something bigger.

## What you can ask it to do

```
Tell sf-admin to add a Currency field named Sale_Price to Car__c.
```
Routes straight to `sf-admin` — no ceremony, it builds and deploys the field.

```
Solve this case study with the full team: build a many-to-many relationship
between Car and Promotion, add the required automation, and audit the
existing flows.
```
No specific role named → Business Analyst clarifies requirements, Architect decides the data model (and explains what it rejected and why), Admin builds it, Release Manager verifies and writes the final report.

```
Tell sf-qa-adversarial to try to break the automation I just built.
```
Deliberately stress-tests what was just built — bulk inserts, edge cases, negative values — before you consider it done.

```
Tell sf-practice-generator to seed a certification practice scenario for Apex
trigger bulkification.
```
Plants a small, self-contained object with a real, verified bug for you to find and fix yourself — then ask `sf-practice-grader` to check your work against the live org once you're done.

## Requirements

- [Claude Code](https://code.claude.com) installed
- [Salesforce CLI](https://developer.salesforce.com/tools/salesforcecli) (`sf`) installed
- A Salesforce org authenticated locally:
  ```bash
  sf org login web --alias my-org
  sf config set target-org=my-org
  ```

These agents call `sf` directly via Bash — they are not a hosted service, they run against whatever org you've authenticated locally. Point them at a Developer Edition or sandbox org while you're getting a feel for them; they make real changes, not simulations.

## Install

```bash
/plugin marketplace add aydintongur/salesforce-ai-team-plugin
/plugin install salesforce-ai-team@salesforce-ai-team-marketplace
```

Or for local testing:

```bash
claude --plugin-dir /path/to/this/plugin
```

**After installing, restart your Claude Code session** — newly added subagents aren't picked up mid-session, only on a fresh start. If you ask for a role by name and get "Agent type not found," this is almost always why.

Full project (case studies, MCP server, daily health-check job): [github.com/aydintongur/salesforce-ai-team](https://github.com/aydintongur/salesforce-ai-team)

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
| `sf-practice-generator` | Seeds a self-contained certification/interview practice scenario with deliberately planted, verified-real bugs |
| `sf-practice-grader` | Scores a practice attempt by re-checking the live org state against the scenario's answer key |

## Troubleshooting

**"Agent type not found: sf-xyz"** — restart your Claude Code session. New/updated subagents in `.claude/agents/` only load at session start, not mid-conversation.

**A deploy fails with "This schedulable class has jobs pending or in progress"** — you're redeploying an Apex class tied to a currently-scheduled job. Abort it first (`System.schedule`'s Id, via `System.abortJob('<id>')` in Execute Anonymous), redeploy, then reschedule.

**A field you just created "doesn't exist" in Apex or reports** — Metadata/Tooling API field creation doesn't grant field-level security automatically the way the Setup UI wizard does. Grant FLS to the relevant profile/permission set as an explicit next step — `sf-admin` knows to do this, but if you're working around it directly, this is the fix.

**Deploying a change to an existing custom object fails oddly around relationships** — check whether the object is a master-detail child (e.g. rolls up to Account). Granting access or making certain changes to a master-detail child can require the parent object's permissions to already be in place.

## A note on scope

Each role deploys real metadata and can run real Apex against whatever org your local `sf` CLI is authenticated to. These agents make real changes — review what they propose if you're working against anything other than a disposable practice org.
