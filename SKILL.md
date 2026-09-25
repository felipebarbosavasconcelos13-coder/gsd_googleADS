---
name: google-ads-skill-orchestrator
description: "Reusable orchestration skill for Google Ads projects. Routes requests to the minimum necessary specialist skills and workflows, uses AdLoop MCP for live Google Ads, GA4, Search Console, Merchant Center, GTM and PageSpeed evidence when available, coordinates multi-step diagnosis and planning, and enforces safe two-phase account changes."
category: workflow
risk: controlled
source: custom
tags:
  - google-ads
  - orchestration
  - adloop
  - mcp
  - ga4
  - search-console
  - merchant-center
  - gtm
  - tracking
  - optimization
---

# Google Ads Skill Orchestrator

## Mission

This is the primary orchestration skill for Google Ads projects.

Its responsibility is to decide:

1. what the user actually wants
2. which specialist skill, workflow or live-data tool is required
3. what evidence must be collected before making a conclusion
4. what order multiple capabilities should run in
5. when a proposed change needs preview, dry run, approval and verification
6. when the task is complete and orchestration should stop

The orchestrator does not replace specialist skills and does not replace MCP tools.

Skills provide domain reasoning and playbooks. AdLoop MCP provides supported live data, planning tools and account actions.

## Core Rule

Use the minimum sufficient capability.

Do not load every potentially relevant skill.

Preferred pattern:

`request -> classify -> discover capabilities -> gather evidence -> route -> validate -> plan -> preview/dry-run if needed -> approval -> execute -> verify -> stop`

Expand the skill chain only when evidence or a real dependency requires another specialist.

## Scope

Use this skill for Google Ads and directly related measurement or post-click work, including:

- Google Ads account and campaign analysis
- Search campaigns
- Performance Max
- Shopping and Merchant Center
- keywords, search terms and negatives
- audiences and remarketing
- RSA copy and ad assets
- bidding and budgets
- Auction Insights and competitive analysis
- Quality Score
- landing pages and PageSpeed
- conversion tracking
- enhanced conversions
- offline conversion imports
- attribution
- GA4 analysis related to paid media
- Google Search Console analysis related to paid-search decisions
- GTM diagnosis related to Google Ads measurement

Do not use this skill as a general software-development orchestrator.

## Capability Discovery

Before routing, inspect what is actually available in the current environment.

Potential capabilities include:

- locally installed Google Ads specialist skills
- `adloop-workflows`, when installed
- AdLoop MCP, when installed and connected
- project files and client configuration
- other legitimate local tools when AdLoop does not cover the required operation

Do not assume every companion skill is installed.

If `adloop-workflows` is unavailable, use `references/diagnostic-playbooks.md` as the workflow backbone.

Do not install or create new skills unless the user explicitly asks for that.

## Source of Truth Precedence

When evidence or instructions conflict, use this priority:

1. live platform state returned by connected tools
2. current tool and API capability constraints
3. explicit project or client configuration
4. specialist-skill guidance
5. generic best practices

A specialist skill may contain useful heuristics, but it must not override live evidence or current platform limitations.

Never present a generic threshold as a confirmed platform rule unless current evidence supports it.

## Task Classification

### Level 0: Generic project task

Use normal agent capabilities when no Google Ads expertise is required.

Examples:

- edit Markdown
- rename a file
- inspect JSON
- update documentation

### Level 1: Specific specialist task

Use one specialist when the request clearly belongs to one domain.

Examples:

- RSA copy -> `ad-copy-generation`
- audiences -> `audience-targeting`
- budget allocation -> `budget-optimization`
- Auction Insights -> `competitor-analysis`
- conversion infrastructure -> `conversion-tracking`
- full account audit -> `google-ads-audit`
- keywords, search terms or negatives -> `keyword-research`
- landing page -> `landing-page-audit`
- PMax -> `pmax-optimization`
- Quality Score -> `quality-score-optimization`
- remarketing -> `remarketing-strategy`
- Shopping or feed strategy -> `shopping-ads`

Use the specialist directly when it can solve the request without a broader workflow.

For ambiguous routing, read `references/specialist-routing.md`.

### Level 2: Operational workflow

For broad or multi-step work, prefer the matching `adloop-workflows` playbook when installed:

- performance diagnosis -> `analyze-performance.md`
- budget planning -> `budget-plan.md`
- complete ad creation -> `create-ad.md`
- Search or PMax campaign creation -> `create-campaign.md`
- tracking diagnosis -> `diagnose-tracking.md`
- campaign optimization -> `optimize-campaign.md`

If `adloop-workflows` is unavailable, use `references/diagnostic-playbooks.md`.

### Level 3: Multi-skill task

Use one workflow or playbook as the backbone and add specialists progressively.

Example:

`CPA increased -> performance diagnosis -> live evidence -> search-term deterioration found -> keyword-research -> stop if root cause and action are clear`

Do not automatically add competitor, landing-page, tracking and budget analysis to every performance problem.

## Dependency Rules

Use these dependencies to prevent bad sequencing:

- validate conversion measurement before making major budget or Smart Bidding decisions when conversion data looks suspicious
- check existing search terms and negative coverage before adding more keywords to an established campaign
- check campaign health, keyword relevance and landing-page alignment before assuming more ad copy is the solution
- route Quality Score findings by component: Expected CTR -> copy, Ad Relevance -> keyword/ad-group alignment, Landing Page Experience -> landing page
- remarketing strategy depends on working audience and event measurement
- Shopping and ecommerce PMax delivery problems should check Merchant Center feed health before blaming bids or budgets
- competitor findings should influence strategy only when combined with campaign economics and performance evidence
- broad-match additions require compatible Smart Bidding context, never assume they are safe by default

## Preferred Live Data Provider: AdLoop MCP

When live Google marketing data is required, prefer AdLoop MCP when it is connected and supports the operation.

AdLoop may provide access to:

- Google Ads
- Google Analytics 4
- Google Search Console
- Google Merchant Center
- Google Tag Manager
- PageSpeed Insights
- combined Ads + GA4 diagnostics
- keyword planning and budget forecasting
- controlled Google Ads write operations

Before asking the user to manually export platform data, determine whether AdLoop can retrieve it.

For tool selection, limitations and exact safety behavior, read `references/adloop-mcp.md`.

## Evidence-First Policy

Do not recommend a material Google Ads change solely because it is considered a generic best practice.

Recommendations should be grounded in available evidence such as:

- live campaign configuration
- performance metrics
- conversion integrity
- search terms
- keyword performance
- Auction Insights or competitive metrics
- audience performance
- budget behavior
- landing-page behavior
- GA4 data
- Search Console data
- Merchant Center feed health
- GTM configuration
- PageSpeed data

Always distinguish:

- observed fact
- inferred cause
- hypothesis requiring validation
- recommendation

Do not present an unverified hypothesis as a confirmed root cause.

## Time and Comparison Discipline

When evaluating performance:

1. identify the date range
2. choose an equivalent comparison period when comparison is needed
3. account for incomplete periods
4. consider conversion lag and recent account changes
5. consider seasonality when relevant
6. avoid mixing data sources with materially different freshness windows without noting it

Read `references/data-interpretation.md` for known source limitations and interpretation rules.

## Progressive Routing

Do not preload every possible specialist.

Example, CPA increased:

1. run performance diagnosis
2. inspect campaign and conversion evidence
3. if search-term quality deteriorated, route to `keyword-research`
4. if competitive pressure is supported by evidence, route to `competitor-analysis`
5. if measurement is inconsistent, route to `conversion-tracking` and use GA4/GTM evidence
6. if allocation is inefficient after measurement is trusted, route to `budget-optimization`
7. stop when the root cause and next action are sufficiently supported

## Multi-Skill Execution Protocol

When multiple capabilities are needed:

1. define the final objective
2. select the workflow backbone if needed
3. identify the minimum initial specialists
4. determine dependencies
5. gather evidence before corrective actions
6. pass only relevant findings to downstream specialists
7. avoid duplicate analysis
8. validate conclusions against live data when available
9. create a precise implementation plan for material changes
10. follow write-safety rules
11. verify live state after execution
12. return one consolidated result
13. stop

## Context Efficiency

For account-wide analysis, prefer summarized or compact tool output when available.

Use full-detail queries only when drilling into a specific campaign, ad group, keyword, ad, asset or resource that may require a decision or mutation.

Do not flood the context with raw tables if totals, top offenders and targeted drill-downs answer the question.

## Mutation Safety

Google Ads changes can affect spend, traffic, leads, sales and measurement.

For material changes, the default sequence is:

`read current state -> define exact change -> generate preview/plan -> show scope -> dry run when supported -> explicit approval -> real apply -> verify -> log`

A preview, plan or dry run is not authorization to modify the live account.

When AdLoop returns a `plan_id`, that exact plan is the approval boundary.

Do not apply a materially different plan under an earlier approval.

For detailed rules, read `references/write-safety.md` before any account mutation.

## Project Portability

This skill must remain reusable across clients and projects.

Do not hardcode inside the skill:

- Google Ads customer IDs
- campaign IDs
- ad group IDs
- GA4 property IDs
- Search Console properties
- Merchant Center account IDs
- GTM account or container IDs
- client names
- budgets
- conversion action IDs
- credentials or tokens

Resolve project-specific values from live discovery, project configuration or explicit user input.

## Missing Data

If required information is unavailable, do not invent it.

Try relevant sources in this order:

1. AdLoop curated read or cross-reference tool
2. AdLoop targeted GAQL/report query
3. GA4, GSC, Merchant Center, GTM or PageSpeed through AdLoop when relevant
4. project files
5. another legitimate local provider
6. ask the user only when the information cannot reasonably be retrieved

## Conflicting Skills

If two specialist skills recommend conflicting actions:

1. re-evaluate the user's objective
2. prefer current live evidence
3. prefer the specialist closest to the actual problem
4. consider upstream dependencies
5. avoid executing conflicting actions simultaneously
6. surface uncertainty when evidence is insufficient

The orchestrator owns routing conflict resolution.

## Stop Conditions

Stop invoking additional skills when:

- the user's objective is satisfied
- the root cause is sufficiently identified
- enough evidence exists to choose the next action
- the implementation plan is complete
- approved execution is complete and verified
- another specialist would only repeat existing analysis

More skills do not automatically produce a better result.

## References

Load references only when needed:

- `references/specialist-routing.md`: detailed map of the 12 Google Ads specialist skills and their dependencies
- `references/adloop-mcp.md`: AdLoop tool map, planning tools, cross-platform tools, limitations and execution model
- `references/diagnostic-playbooks.md`: operational sequences for common Google Ads tasks
- `references/write-safety.md`: mutation, preview, dry-run, approval and verification rules
- `references/data-interpretation.md`: source freshness, GDPR, PMax, GSC, Merchant and other interpretation caveats

## Final Decision Sequence

For every Google Ads request, ask:

1. What outcome does the user want?
2. Is this generic or Google Ads specific?
3. Can one specialist solve it?
4. If not, which workflow or diagnostic playbook should control the process?
5. What live evidence is required?
6. Can AdLoop retrieve that evidence?
7. What does the evidence actually show?
8. Is another specialist genuinely necessary?
9. Does the task require a live account change?
10. Has the exact change been previewed and dry-run when supported?
11. Has the user explicitly approved that exact scope?
12. Was the final state verified?
13. Can orchestration stop now?

If yes, stop.