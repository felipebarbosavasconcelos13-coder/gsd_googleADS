# Google Ads Specialist Routing

Use this reference when selecting among the Google Ads specialist skills from `itallstartedwithaidea/agent-skills`.

The orchestrator should prefer locally installed copies of these skills when available. This reference describes when to invoke them, what evidence they need, and how they depend on each other.

## 1. ad-copy-generation

Primary use:

- RSA headlines and descriptions
- ad refreshes
- A/B variants
- DKI decisions
- pinning strategy
- ad relevance improvement

Inputs that improve quality:

- target keywords
- landing page
- value propositions
- audience
- existing ad performance
- competitor messaging when relevant

Dependencies:

- `keyword-research` when keyword intent or ad-group themes are unclear
- `landing-page-audit` when message match is weak
- `quality-score-optimization` when Expected CTR or Ad Relevance is the actual problem
- `competitor-analysis` when differentiation is the objective

Routing rule:

Do not generate new ads merely because CTR is weak. First determine whether low CTR is caused by targeting, weak query intent, low rank, poor offer, or creative.

When deploying through AdLoop, respect RSA character limits and verify final URLs before drafting.

## 2. audience-targeting

Primary use:

- in-market audiences
- affinity and custom segments
- Customer Match strategy
- demographic observation or exclusions
- PMax audience signals
- audience layering

Dependencies:

- `conversion-tracking` when audience membership depends on events
- `remarketing-strategy` for lifecycle and recency segmentation
- `pmax-optimization` for PMax signal strategy

Important constraint:

Do not assume every audience type can be attached to every campaign type. Current platform capability and AdLoop results take precedence over generic skill examples.

## 3. budget-optimization

Primary use:

- budget pacing
- campaign allocation
- budget redistribution
- portfolio thinking
- marginal-return analysis
- budget sufficiency
- seasonal planning

Dependencies:

- `conversion-tracking` before optimizing around CPA/ROAS when measurement is questionable
- `google-ads-audit` for structural blockers
- `competitor-analysis` when impression share or CPC pressure may explain constraints
- `quality-score-optimization` when inefficient spend is caused by poor quality rather than budget

Routing rule:

Never optimize budget from ROAS or CPA data that has not been validated when tracking anomalies are suspected.

Use AdLoop `estimate_budget` for Keyword Planner forecasts when planning a Search launch or materially changing budget assumptions.

## 4. competitor-analysis

Primary use:

- Auction Insights
- impression share
- overlap rate
- position-above rate
- outranking share
- competitive CPC pressure
- competitor trend analysis
- messaging differentiation

Dependencies:

- `keyword-research` for coverage gaps
- `ad-copy-generation` for message differentiation
- `budget-optimization` when competitive pressure materially affects allocation

Routing rule:

Do not infer competitor budgets, motives or bidding strategy as fact from Auction Insights alone. Label such conclusions as hypotheses.

Do not enter bidding wars simply because a competitor appears aggressive. Combine competition data with margin, CPA/ROAS and Quality Score evidence.

## 5. conversion-tracking

Primary use:

- Google Ads conversion actions
- Google Tag
- enhanced conversions
- offline conversion imports
- CRM attribution
- conversion validation
- attribution diagnosis
- conversion hierarchy

Dependencies:

- AdLoop GA4 and GTM capabilities for live validation
- project codebase when event instrumentation must be inspected

Routing rule:

When conversions drop unexpectedly, validate measurement before changing campaign strategy.

Use AdLoop cross-reference tools first when they directly answer Ads vs GA4 discrepancies.

Do not rely on hardcoded eligibility thresholds from generic skill examples when current Google platform rules differ.

## 6. google-ads-audit

Primary use:

- full account audits
- broad diagnostics
- structural review
- wasted-spend detection
- optimization backlog
- campaign/account health review

Potential domains surfaced by an audit:

- campaign structure
- bidding
- keywords
- ad copy
- PMax
- Shopping
- audiences
- conversions
- budget
- Quality Score
- search terms
- landing pages
- competitive strategy
- policy or operational issues

Routing rule:

Use it for broad account questions, not every bounded request.

After the audit identifies an issue, hand off to the relevant specialist instead of duplicating the specialist's analysis inside the audit.

Use compact live-data calls for the first account-wide pass when available.

## 7. keyword-research

Primary use:

- keyword discovery
- search intent
- search-term mining
- negative keywords
- match-type decisions
- ad-group themes
- long-tail expansion
- keyword coverage gaps

Dependencies:

- `quality-score-optimization` when relevance or QS is poor
- `competitor-analysis` for competitive coverage gaps
- `ad-copy-generation` when new themes require aligned ads

Routing rules:

- inspect existing search terms before adding more keywords to a mature campaign
- check current negatives before proposing duplicates
- Broad Match requires compatible Smart Bidding context
- do not label every informational query as negative; assess business value and funnel role
- negative-keyword decisions should be based on relevance and economics, not zero conversions alone when sample size is weak

Use AdLoop `discover_keywords` for Keyword Planner discovery when available.

## 8. landing-page-audit

Primary use:

- paid landing-page performance
- CRO
- message match
- mobile experience
- CTA and form friction
- Core Web Vitals
- landing-page Quality Score issues

Dependencies:

- `quality-score-optimization` when landing-page experience is a QS component problem
- `ad-copy-generation` for ad-to-page message alignment
- `conversion-tracking` when low CVR may actually be measurement failure

Preferred live evidence:

- AdLoop `landing_page_analysis`
- GA4 page behavior
- AdLoop `analyze_page_speed`
- current ad final URLs

Interpretation rule:

When PageSpeed field data exists, treat it as real-user evidence. Missing field data may mean insufficient traffic, not good performance.

## 9. pmax-optimization

Primary use:

- Performance Max structure
- asset groups
- asset quality
- search themes
- audience signals
- URL expansion
- brand exclusions
- PMax delivery diagnosis

Dependencies:

- `shopping-ads` for feed-driven PMax
- `audience-targeting` for signal strategy
- `ad-copy-generation` for text assets
- `budget-optimization` for allocation
- `conversion-tracking` for measurement integrity

Critical current-platform constraints:

- do not claim full Search vs YouTube vs Display vs Discover channel splits when the Google Ads API does not provide them
- do not assume per-asset BEST/GOOD/LOW labels are exposed by the API; use AdLoop detailed asset combinations and available signals instead
- PMax audience targeting is automated and may not appear like explicit Search audience targeting

For ecommerce PMax with delivery collapse or weak volume, check Merchant Center feed health before campaign-side changes.

## 10. quality-score-optimization

Primary use:

- Quality Score diagnosis
- Expected CTR
- Ad Relevance
- Landing Page Experience
- spend-weighted QS prioritization

Routing by component:

- Expected CTR issue -> `ad-copy-generation`, plus query and audience context
- Ad Relevance issue -> `keyword-research`, ad-group structure, copy alignment
- Landing Page Experience issue -> `landing-page-audit`

Routing rule:

Do not treat Quality Score as the final business KPI. Prioritize QS work where it materially affects spend, rank, CPC or conversion economics.

Do not present generic CPC-savings percentages as guaranteed outcomes.

## 11. remarketing-strategy

Primary use:

- remarketing architecture
- RLSA
- Display and YouTube remarketing
- dynamic remarketing
- recency windows
- frequency strategy
- sequential messaging
- post-conversion exclusions

Dependencies:

- `audience-targeting` for list/segment design
- `conversion-tracking` for event and converter boundaries
- `ad-copy-generation` for stage-specific messaging
- `shopping-ads` for ecommerce dynamic remarketing when feed data is involved

Routing rule:

Do not assume fixed frequency caps or bid adjustments are universally optimal. Treat generic numbers from the specialist skill as starting hypotheses and validate against campaign/channel behavior.

## 12. shopping-ads

Primary use:

- Shopping campaign strategy
- Merchant Center
- product feed health
- feed title/description strategy
- custom labels
- product segmentation
- ecommerce PMax feed dependencies

Dependencies:

- `pmax-optimization` for Performance Max Shopping inventory
- `competitor-analysis` when pricing or competition affects performance
- `remarketing-strategy` for dynamic ecommerce remarketing

Preferred live evidence:

- AdLoop `list_merchant_accounts`
- AdLoop `get_merchant_feed_health`
- Google Ads product/campaign evidence when available

Important constraint:

AdLoop Merchant Center tools are read-only. Do not promise feed edits through AdLoop when the MCP does not expose them.

## Routing Matrix

| Intent | Primary specialist | Supporting specialist only when evidence requires it |
|---|---|---|
| Create/improve RSA | `ad-copy-generation` | `keyword-research`, `landing-page-audit`, `quality-score-optimization`, `competitor-analysis` |
| Audience strategy | `audience-targeting` | `remarketing-strategy`, `conversion-tracking`, `pmax-optimization` |
| Budget allocation | `budget-optimization` | `conversion-tracking`, `google-ads-audit`, `competitor-analysis` |
| Auction/competition | `competitor-analysis` | `keyword-research`, `ad-copy-generation`, `budget-optimization` |
| Tracking/conversions | `conversion-tracking` | GA4/GTM through AdLoop |
| Full account audit | `google-ads-audit` | route by findings |
| Keywords/search terms/negatives | `keyword-research` | `quality-score-optimization`, `competitor-analysis`, `ad-copy-generation` |
| Landing page | `landing-page-audit` | `quality-score-optimization`, `conversion-tracking`, `ad-copy-generation` |
| PMax | `pmax-optimization` | `shopping-ads`, `audience-targeting`, `budget-optimization`, `conversion-tracking` |
| Quality Score | `quality-score-optimization` | route by component |
| Remarketing | `remarketing-strategy` | `audience-targeting`, `conversion-tracking`, `ad-copy-generation` |
| Shopping/feed | `shopping-ads` | `pmax-optimization`, `competitor-analysis`, `remarketing-strategy` |

## General Specialist Rule

Use each specialist for its domain knowledge, not as an automatic action generator.

Before applying any recommendation:

1. verify current live state
2. confirm the recommendation is compatible with the current platform
3. validate that enough evidence exists
4. respect write-safety rules
