# Write Safety Reference

Use this reference before any material Google Ads mutation.

## Core sequence

`read -> diagnose -> define change -> preview -> show user -> dry run -> approve -> apply -> verify -> log`

A preview is not an execution.

A dry run is not an execution.

Approval applies only to the exact plan shown to the user.

## Material changes requiring explicit approval

Examples:

- create, pause, enable or remove campaigns
- create, pause, enable or remove ad groups
- create or change ads
- add or remove keywords
- add or remove negative keywords
- attach or detach shared negative lists
- change bids or bidding strategy
- change budgets
- change geo/language targeting
- change demographic targeting
- change conversion configuration
- mark GA4 events as key events
- materially alter Shopping/PMax configuration

## AdLoop two-phase execution

When AdLoop returns a `plan_id`:

1. treat that exact plan as the approval boundary
2. show the preview before any `confirm_and_apply`
3. run `confirm_and_apply(..., dry_run=true)` first when supported/required
4. inspect the dry-run result
5. only use `dry_run=false` after explicit approval for the same plan
6. verify live state after execution

Do not retry real apply in a loop when AdLoop returns a safety status such as `DRY_RUN_REQUIRED`.

## One change at a time

Prefer one logical mutation at a time.

For a multi-step optimization:

1. draft first change
2. preview
3. approve
4. apply
5. verify
6. continue to next change

This improves auditability and reduces unintended interactions.

## Never guess IDs

Retrieve IDs from live read tools before write tools.

Examples:

- campaign IDs from campaign reads
- ad group IDs from GAQL/current structure
- ad IDs from ad reads
- shared set IDs from negative-list reads
- demographic criterion removal IDs from demographic reads

## Irreversible actions

Prefer pause over remove when either satisfies the user's objective.

Use permanent removal only when explicitly requested and the impact is understood.

## Broad Match safety

Before proposing or drafting BROAD keywords:

1. inspect campaign bidding strategy
2. confirm compatible Smart Bidding context
3. if incompatible, use PHRASE/EXACT or resolve bidding strategy first

Never assume Broad Match is safe merely because a specialist skill recommends expansion.

## URL safety

Before drafting RSAs, sitelinks or other URL-bearing assets:

- verify the URL exists and is reachable
- do not invent final URLs or display paths
- inspect message match when relevance is material

## Measurement-first safety

Before scaling budget, switching to conversion-oriented bidding, or adding major traffic volume when conversion data appears suspicious:

1. validate tracking
2. reconcile Ads and GA4 when possible
3. inspect GTM/code events when necessary
4. separate consent effects from true tracking failures

## Targeting replacement risk

Some update operations replace the supplied targeting list rather than append to it.

Before changing geo, language, demographics or other targeting:

- read current state
- compare old and proposed states
- ensure preserved exclusions are visible in the preview
- do not assume omitted entries remain

## Budget safety

Respect tool-configured caps and business constraints.

A forecast or generic best-practice multiplier is not authorization to increase spend.

## Verification after apply

After every real write:

1. re-read the affected resource
2. confirm correct account
3. confirm correct campaign/ad group/entity
4. confirm intended old -> new state
5. detect partial failure
6. report follow-up monitoring needed

Never claim success solely because the mutation call returned no exception.
