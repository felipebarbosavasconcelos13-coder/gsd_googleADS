# GSD Google Ads Skill Orchestrator

Reusable orchestration skill for Google Ads projects.

This repository contains a project-independent Google Ads meta-skill designed to coordinate specialist Google Ads skills, operational playbooks and AdLoop MCP capabilities.

The goal is simple: the user should describe the business problem in natural language, and the orchestrator should decide which workflow, specialist skill and live-data tools are actually needed.

## What this skill does

The orchestrator can:

- classify Google Ads requests by intent and complexity
- route narrow tasks directly to the appropriate specialist skill
- use broader operational playbooks for multi-step work
- progressively invoke only the additional skills justified by evidence
- retrieve live data through AdLoop MCP when available
- combine Google Ads evidence with GA4, Google Search Console, Merchant Center, GTM and PageSpeed when relevant
- separate observed facts from hypotheses and recommendations
- coordinate planning before live account changes
- enforce preview, dry-run, approval and verification for material mutations
- remain portable across clients by avoiding hardcoded account IDs and project-specific values

## Architecture

```text
User request
    |
    v
Google Ads Skill Orchestrator
    |
    +--> specific task --> specialist skill
    |
    +--> broad task --> operational workflow / diagnostic playbook
    |
    v
Live evidence
    |
    +--> Google Ads
    +--> GA4
    +--> Search Console
    +--> Merchant Center
    +--> GTM
    +--> PageSpeed
    |
    v
Additional specialist only when justified
    |
    v
Recommendation or controlled execution
```

## Source-of-truth priority

When instructions or assumptions conflict, the orchestrator follows this precedence:

1. live platform state returned by connected tools
2. current API and MCP capability constraints
3. explicit project/client configuration
4. specialist-skill guidance
5. generic best practices

This is important because some community skills contain useful heuristics that may not match current Google Ads API limitations.

## Specialist skills supported

The orchestrator is designed to work with these Google Ads specialist skills when they are installed:

- `ad-copy-generation`
- `audience-targeting`
- `budget-optimization`
- `competitor-analysis`
- `conversion-tracking`
- `google-ads-audit`
- `keyword-research`
- `landing-page-audit`
- `pmax-optimization`
- `quality-score-optimization`
- `remarketing-strategy`
- `shopping-ads`

These skills were studied from:

`itallstartedwithaidea/agent-skills/tree/main/skills/google-ads`

The orchestrator does not blindly follow every recommendation from those skills. It uses them as domain expertise while giving precedence to live evidence and current platform capabilities.

## AdLoop MCP integration

AdLoop is the preferred live-data and execution provider when installed and connected.

Repository studied:

`kLOsk/adloop`

The orchestrator uses AdLoop for supported access to:

- Google Ads
- Google Analytics 4
- Google Search Console
- Google Merchant Center
- Google Tag Manager
- PageSpeed Insights
- Ads + GA4 cross-reference analysis
- keyword planning
- budget forecasting
- controlled Google Ads write operations

### Important AdLoop behaviors incorporated

The orchestrator understands several AdLoop-specific behaviors and constraints, including:

- use `compact=true` for broad account audits when supported
- drill down to full rows only for entities that require a decision or write
- use `analyze_campaign_conversions`, `landing_page_analysis` and `attribution_check` for cross-platform analysis when appropriate
- check Merchant Center feed health before blaming bids or budgets for Shopping/PMax delivery issues
- consider GDPR/consent gaps before diagnosing Ads vs GA4 discrepancies as broken tracking
- use `health_check` when connectivity or authorization is uncertain
- do not guess entity IDs
- verify URLs before creating ads or sitelinks
- use Broad Match only in a compatible Smart Bidding context
- reuse shared negative keyword lists when appropriate
- prefer pausing over irreversible deletion
- respect AdLoop's preview / `plan_id` / dry-run / explicit approval flow

## Operational workflows

When `adloop-workflows` is installed, the orchestrator can use these playbooks:

- `analyze-performance.md`
- `budget-plan.md`
- `create-ad.md`
- `create-campaign.md`
- `diagnose-tracking.md`
- `optimize-campaign.md`

If those workflow files are not installed locally, this repository includes equivalent guidance in:

`references/diagnostic-playbooks.md`

## Routing examples

### CPA increased

```text
performance diagnosis
    -> inspect campaign + conversion evidence
    -> search-term deterioration found
    -> keyword-research
    -> stop when root cause and action are clear
```

### Conversion drop

```text
diagnose tracking
    -> attribution_check
    -> GA4 / GTM evidence if needed
    -> conversion-tracking specialist
```

### New Search campaign

```text
create-campaign workflow
    -> keyword-research
    -> budget planning
    -> ad-copy-generation
    -> conversion readiness check
    -> preview
    -> approval
    -> apply
```

### PMax underperforming

```text
performance diagnosis
    -> get_pmax_performance
    -> Merchant Center feed health if ecommerce
    -> pmax-optimization
    -> audience-targeting / budget-optimization only if evidence requires
```

### Quality Score problem

```text
quality-score-optimization
    -> Expected CTR issue -> ad-copy-generation
    -> Ad Relevance issue -> keyword-research / ad-group alignment
    -> Landing Page Experience issue -> landing-page-audit
```

## Safety model

Material Google Ads changes follow this sequence:

```text
read current state
    -> define exact change
    -> generate preview / plan
    -> show scope
    -> dry run when supported
    -> explicit user approval
    -> real apply
    -> verify live state
    -> log if required
```

A preview or dry run is not authorization to modify the live account.

When AdLoop returns a `plan_id`, that exact plan becomes the approval boundary.

The orchestrator should never silently expand the scope after approval.

## Repository structure

```text
gsd_googleADS/
├── SKILL.md
├── README.md
├── LICENSE
└── references/
    ├── specialist-routing.md
    ├── adloop-mcp.md
    ├── diagnostic-playbooks.md
    ├── write-safety.md
    └── data-interpretation.md
```

### `SKILL.md`

Main orchestration rules and decision logic.

### `references/specialist-routing.md`

Detailed map of the 12 specialist Google Ads skills, their use cases, dependencies and routing interactions.

### `references/adloop-mcp.md`

AdLoop capability map, read tools, planning tools, cross-reference tools, limitations and write workflow.

### `references/diagnostic-playbooks.md`

Operational sequences for common Google Ads tasks such as performance analysis, campaign creation, tracking diagnosis, optimization and budget planning.

### `references/write-safety.md`

Rules for preview, dry run, approval, write execution, irreversible actions and verification.

### `references/data-interpretation.md`

Known interpretation caveats for GA4, GDPR/consent, Search Console freshness, Merchant Center lag, PMax API limitations and other source-specific behaviors.

## Installing in Antigravity

For a project-local installation, place the skill in:

```text
<project>/.agent/skills/google-ads-skill-orchestrator/
```

Recommended structure:

```text
<project>/
└── .agent/
    └── skills/
        └── google-ads-skill-orchestrator/
            ├── SKILL.md
            └── references/
                ├── specialist-routing.md
                ├── adloop-mcp.md
                ├── diagnostic-playbooks.md
                ├── write-safety.md
                └── data-interpretation.md
```

The companion specialist skills may be installed globally or locally, depending on the agent environment.

## Companion capabilities

Recommended but not mandatory:

- AdLoop MCP
- `adloop-workflows`
- the 12 Google Ads specialist skills listed above

The orchestrator should discover what is actually available at runtime and degrade gracefully when a companion capability is absent.

## Portability rules

Do not hardcode inside this repository:

- Google Ads customer IDs
- campaign IDs
- ad group IDs
- GA4 property IDs
- Search Console properties
- Merchant Center account IDs
- GTM account/container IDs
- client names
- budgets
- conversion action IDs
- credentials
- tokens

Those belong in the project or must be discovered at runtime.

## Design principles

- minimum sufficient skill set
- evidence before recommendation
- live state before generic heuristics
- progressive routing
- no duplicate analysis
- no fabricated data
- no guessed IDs
- no blind external skill discovery
- no automatic skill creation
- safe writes only
- stop when the task is solved

## Current focus

This repository is intentionally focused on Google Ads and directly related measurement systems.

It is not intended to be a general-purpose software development orchestrator.

## References studied

Primary external sources used to design this orchestrator:

- `https://github.com/kLOsk/adloop`
- `https://github.com/itallstartedwithaidea/agent-skills/tree/main/skills/google-ads`

The repository does not copy those projects wholesale. It consolidates routing and orchestration knowledge around the user's reusable Google Ads workflow.
