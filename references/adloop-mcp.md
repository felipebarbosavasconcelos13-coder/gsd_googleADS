# AdLoop MCP Reference

Primary upstream source: `https://github.com/kLOsk/adloop`

Use this reference when live platform data, planning, diagnostics or Google Ads account actions are required.

## Responsibility split

The orchestrator decides what the task requires, which specialist or playbook should be used, and what evidence must be collected.

AdLoop MCP provides supported live data, planning tools, cross-platform diagnostics and controlled account mutations.

## Connectivity

Use `health_check` when AdLoop tools fail or authorization is uncertain.

Do not treat a permission, OAuth or connectivity failure as evidence about campaign performance.

## Google Ads read layer

Common tools include:

- `list_accounts`
- `get_campaign_performance`
- `get_ad_performance`
- `get_keyword_performance`
- `get_search_terms`
- `get_negative_keywords`
- `get_negative_keyword_lists`
- `get_negative_keyword_list_keywords`
- `get_negative_keyword_list_campaigns`
- `get_recommendations`
- `get_pmax_performance`
- `get_asset_performance`
- `get_detailed_asset_performance`
- `get_audience_performance`
- `get_demographic_targeting`
- `run_gaql`

For account-wide audits, prefer `compact=true` where supported. Use full mode only when a specific entity must be inspected or may be changed.

Do not manually divide `cost_micros` when AdLoop already returns normalized cost metrics.

## Google Analytics 4

Common tools:

- `get_account_summaries`
- `run_ga4_report`
- `run_realtime_report`
- `get_tracking_events`

Use GA4 for sessions, users, landing-page behavior, events, key events, paid-traffic validation and realtime verification.

## Ads + GA4 cross-reference tools

Prefer these when the task spans both systems:

- `analyze_campaign_conversions`
- `landing_page_analysis`
- `attribution_check`

These reduce duplicate querying and help distinguish campaign-performance problems from tracking or attribution problems.

## Tracking tools

- `validate_tracking`
- `generate_tracking_code`

Before `validate_tracking`, extract expected event names from the codebase or tracking specification when available.

## Google Tag Manager read tools

Relevant tools include:

- `audit_event_coverage`
- `list_gtm_accounts`
- `list_gtm_containers`
- `list_gtm_tags`
- `get_gtm_tag`
- `list_gtm_triggers`
- `get_gtm_trigger`
- `list_gtm_variables`
- `list_gtm_workspaces`
- `get_gtm_workspace_diff`
- `list_gtm_versions`
- `get_gtm_version`

Use GTM when the diagnosis requires tag, trigger, workspace or publish-history evidence.

## Search Console

- `list_gsc_sites`
- `run_gsc_report`

Use GSC for:

- paid vs organic query overlap
- cannibalization analysis
- keyword opportunity mining
- organic query CTR and position context
- page-level organic visibility

Never guess a Search Console property. Discover it first when unknown.

## PageSpeed

Use `analyze_page_speed` for landing-page technical performance and Core Web Vitals.

Prefer mobile first for paid landing pages unless the task is explicitly desktop-focused.

When field and lab data disagree, field data is the stronger real-user signal. Missing field data may mean insufficient traffic.

## Merchant Center

- `list_merchant_accounts`
- `get_merchant_feed_health`

For Shopping or ecommerce PMax delivery problems, check feed health before changing bids or budgets.

AdLoop Merchant Center tools are read-only. Do not promise feed edits through AdLoop.

## Planning tools

### `discover_keywords`

Use for Keyword Planner discovery from seed keywords and/or a URL.

Useful outputs include monthly volume, competition, bid ranges and seasonality when monthly-volume history is requested.

### `estimate_budget`

Use for Search forecasting before launch or material budget planning.

Treat forecasts as planning estimates, not guaranteed outcomes.

## Google Ads write tools

Current AdLoop versions may expose tools such as:

- `draft_campaign`
- `draft_ad_group`
- `update_campaign`
- `update_ad_group`
- `draft_responsive_search_ad`
- `draft_callouts`
- `draft_structured_snippets`
- `draft_image_assets`
- `draft_sitelinks`
- `draft_keywords`
- `add_negative_keywords`
- `add_negative_locations`
- `draft_key_event`
- `propose_negative_keyword_list`
- `add_to_negative_keyword_list`
- `attach_shared_set_to_campaigns`
- `detach_shared_set_from_campaigns`
- `draft_demographic_targeting`
- `pause_entity`
- `enable_entity`
- `remove_entity`
- `confirm_and_apply`

Do not invent tool names. Inspect the current MCP inventory when execution is required because AdLoop evolves.

## Two-phase write model

Default sequence:

1. inspect current state
2. call the appropriate draft/update/proposal tool
3. receive preview and `plan_id`
4. show the exact preview to the user
5. wait for explicit approval
6. call `confirm_and_apply(plan_id=..., dry_run=true)`
7. inspect the dry-run result
8. obtain explicit approval for real execution if not already unambiguously given for that exact plan
9. call `confirm_and_apply(plan_id=..., dry_run=false)`
10. verify the final live state

A plan is not an executed change.

A dry run is not an executed change.

## Safety constraints learned from AdLoop

- never guess entity IDs, retrieve them first
- prefer pause over irreversible removal unless deletion/removal is explicitly required
- Broad Match must not be added under incompatible non-Smart-Bidding strategies
- verify final URLs before drafting ads or sitelinks
- check conversion health before scaling or making major Smart Bidding changes when measurement is suspicious
- respect configured budget caps
- `two_phase_apply` may require a successful dry-run pass before real execution
- `require_dry_run` may block real writes at configuration level
- destructive operations deserve extra confirmation
- new campaigns and ads may be created paused for review
- campaign targeting requires valid geo and language context
- update operations can replace rather than append some targeting lists, so inspect the preview carefully
- shared negative lists should be reused when appropriate instead of duplicated

## Campaign creation discipline

Before creating a Search campaign:

1. inspect existing campaigns and naming
2. validate measurement readiness
3. define geo and language targets
4. estimate budget when no defensible budget exists
5. choose bidding based on business objective and measurement readiness
6. verify Broad Match compatibility if used
7. preview the full structure
8. create paused when supported
9. add ads/assets
10. enable only after review

## RSA creation discipline

Before drafting an RSA:

- inspect existing ad and keyword context
- verify final URL
- respect headline and description character limits
- prefer diverse assets
- pin only for a real compliance, brand or messaging requirement
- use display paths when appropriate
- do not assume a new ad is the solution if the real problem is tracking, search intent or landing-page relevance

## PMax limitations

Respect current Google Ads API limits:

- do not claim a complete reliable Search vs YouTube vs Display vs Discover split when unavailable
- `MIXED` can represent aggregated PMax network traffic
- do not assume per-asset BEST/GOOD/LOW labels are exposed through the API
- use detailed asset combinations and available quality signals instead
- explicit audience reporting differs from automated PMax audience behavior

## GDPR and consent

Ads clicks and GA4 sessions are not expected to match perfectly.

Consent rejection can reduce GA4-observed sessions and events while Ads still records clicks.

Do not diagnose tracking as broken from clicks > sessions alone.

Use `attribution_check`, GTM evidence, tracking validation and broader source behavior before concluding measurement is broken.

## Data freshness

Search Console data can lag Ads/GA4. Avoid same-day direct comparisons when GSC is incomplete.

Merchant Center product status can lag after feed changes. Do not assume an immediate reflection of a just-updated feed.

## Portability

Do not hardcode client account IDs, property IDs, Merchant IDs, site properties or credentials into this reusable skill.
