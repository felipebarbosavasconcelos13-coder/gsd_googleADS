# Diagnostic Playbooks

These playbooks adapt the operational logic from AdLoop's Google Ads command workflows into reusable orchestration guidance.

Use them when `adloop-workflows` is not installed or when the orchestrator needs a local fallback.

## Performance analysis

Use when the user asks how campaigns are performing, why CPA/ROAS/CPC changed, or where waste exists.

1. Define date range and comparison period.
2. Pull campaign performance, using compact output for account-wide scans.
3. If PMax exists, inspect PMax-specific performance separately.
4. If conversions or CPA are central, use Ads + GA4 cross-reference tools.
5. Drill into keyword/search-term data only when Search is relevant.
6. Validate tracking before attributing sudden conversion drops to media performance.
7. Separate symptoms from root cause.
8. Route to the minimum specialist justified by evidence.

Potential specialist routes:

- search-term waste -> `keyword-research`
- competitive pressure -> `competitor-analysis`
- tracking inconsistency -> `conversion-tracking`
- budget allocation problem -> `budget-optimization`
- poor post-click behavior -> `landing-page-audit`
- PMax-specific issue -> `pmax-optimization`

## Budget planning

Use when the user asks how much to spend or how to distribute budget.

1. Verify conversion measurement if existing performance data will drive the decision.
2. Gather target keywords, geography and language for Search forecasts.
3. Use Keyword Planner discovery/forecasting when relevant.
4. Consider conversion lag and seasonality.
5. Distinguish forecast from guaranteed outcome.
6. Use `budget-optimization` for cross-campaign reallocation or portfolio-level decisions.
7. Avoid large unsupported budget shifts based only on short windows.

## RSA creation

1. Identify target campaign and ad group.
2. Inspect current ads, keywords and campaign health.
3. Verify final URL.
4. If the account has major tracking/relevance problems, surface them before treating new copy as the solution.
5. Route creative reasoning to `ad-copy-generation`.
6. Respect RSA limits and platform validation.
7. Prefer diverse, unpinned assets unless pinning has a real requirement.
8. Preview before any write.

## Search campaign creation

1. Inspect existing structure and naming.
2. Define business objective.
3. Validate conversion readiness.
4. Define target geography and language.
5. Route keyword strategy to `keyword-research`.
6. Estimate budget if none is defensible.
7. Select bidding based on objective and measurement readiness.
8. Verify Broad Match compatibility when Broad is proposed.
9. Create the minimum viable structure.
10. Add ad assets and extensions.
11. Keep new campaign paused until reviewed when the tool supports it.
12. Enable only after approval and validation.

## Tracking diagnosis

1. Do not assume Ads clicks must equal GA4 sessions.
2. Run Ads vs GA4 attribution comparison when available.
3. Consider consent effects before declaring tracking broken.
4. Inspect tracking events.
5. If codebase access exists, extract expected event names.
6. Use tracking validation and GTM coverage tools where relevant.
7. Drill into tags/triggers only for non-healthy events.
8. Check landing pages when traffic exists but conversion behavior does not.
9. Route implementation reasoning to `conversion-tracking`.

## Campaign optimization priority

Default priority when supported by evidence:

1. measurement integrity
2. obvious search-term waste / negatives
3. structural relevance and Quality Score issues
4. ad copy
5. bidding strategy
6. budget allocation

This order is a diagnostic default, not a rigid rule. Live evidence can change the priority.

## Search-term and negative-keyword workflow

1. Pull search terms.
2. Pull existing campaign negatives and shared negative lists.
3. Identify irrelevant intent and quantify waste.
4. Route semantic/match-type reasoning to `keyword-research`.
5. Reuse a suitable shared list if one already exists.
6. Choose campaign-level versus shared-list placement intentionally.
7. Preview proposed negatives before applying.

## Quality Score workflow

1. Pull keyword performance and subcomponents when available.
2. Route by component:
   - Expected CTR -> `ad-copy-generation`
   - Ad Relevance -> `keyword-research` plus ad-group/copy alignment
   - Landing Page Experience -> `landing-page-audit`
3. Prioritize high-spend keywords over low-volume noise.
4. Do not chase Quality Score as an end in itself if business outcomes are healthy.

## PMax workflow

1. Pull PMax performance.
2. Inspect asset groups and available asset-combination signals.
3. For ecommerce, check Merchant Center feed health early.
4. Inspect conversion integrity when performance changes abruptly.
5. Route strategy to `pmax-optimization`.
6. Add `shopping-ads`, `audience-targeting`, `budget-optimization`, or `conversion-tracking` only when evidence justifies them.
7. Respect API transparency limitations. Do not invent unavailable per-channel or per-asset detail.

## Shopping workflow

1. Check Merchant Center account and feed health first.
2. Identify disapprovals or account-level issues.
3. Inspect Shopping/PMax campaign performance after feed health is understood.
4. Route feed/campaign strategy to `shopping-ads`.
5. Use `pmax-optimization` when the products are served through PMax.

## Paid and organic overlap

1. Pull GSC query data and Google Ads keyword/query data for comparable periods.
2. Note GSC freshness lag.
3. Identify queries with strong organic visibility and paid spend.
4. Treat cannibalization as an incrementality question, not an automatic reason to pause paid terms.
5. Use GSC high-impression, weak-position queries as keyword opportunity candidates only after checking paid coverage and business intent.
