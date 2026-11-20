# Game identity and duplicates

Design decision, 2026-11-21. The unit of publication is a launchable edition in a selected Steam profile. The default is one managed shortcut per verified group. A user may split copies, omit the group, or prefer a native Steam entry. Initial coverage may leave many singleton groups. The cost of asking about an ambiguous pair is lower than redirecting someone to a different game or save ecosystem.

## Records and keys

| Record | Key and meaning |
|---|---|
| Work | Local immutable ID for the creative work. Used to navigate related releases. |
| Release | Local immutable ID plus edition, platform, region/language restrictions, original/remake/remaster and launchable content. A work can have multiple releases. Unknown edition is a real state. |
| Product | Store namespace and opaque catalog/SKU identifier, including sandbox/platform when required. Preserve original spelling and aliases. Model bundles, grants, DLC and base products as typed relationships. |
| Entitlement | Store + opaque account + grant ID, or account/product composite if necessary. Record kind, evidence, scope, observation time and explicit expiry/revocation separately. |
| Installation | Machine + connector + local instance + product reference if known. Track present/absent/installing/unknown, launcher readiness and observation freshness. Can exist without an entitlement. |
| External reference | Provider, namespace, opaque ID, relation (same edition, parent, contains, art only), origin, revision, date and confidence. Retain superseded evidence. |
| Publication group | Stable local ID, release members, selected sources, display metadata overrides. One output per profile by default. Keep source records when ungrouping. |
| Steam binding | Steam root + numeric profile + group/output ID, persisted shortcut AppID, original target/name and last-written fields/hashes. Native AppIDs live in a separate read-only index. |

Sharing an executable can indicate a shared base game with unlockable extras. Preserve both SKUs and their paid content. Only treat them as launch-equivalent if edition-level evidence establishes the same playable content and launch behavior, or the user explicitly chooses to group them. Regional censorship, unavailable languages, mutually exclusive installations, different launch targets or incompatible saves merit separate review. Standalone expansions may have their own group. DLC, upgrades, passes and bundles do not independently become shortcuts without an identified launchable product.

## Match policy

1. Generate candidates from indexed external IDs, edition mappings and explicit aliases. A normalized title/search index can find candidates for review. Keep original subtitles, numbers, year and edition tokens.
2. Apply user constraints by stable source IDs before inferred membership. A negative pair blocks joining entire groups across that pair. A manual merge that conflicts with an existing separation needs an explicit resolution.
3. **High** requires a provenance-backed same-edition mapping between both store products, compatible PC launchable kind and no contradictory evidence. Examples include an audited pair of store SKUs or two independently resolved SKU references to the same edition-specific provider ID. An artwork ID or shared base Steam AppID alone can collapse editions and cannot satisfy this rule.
4. **Ambiguous** includes shared parent, same name, inferred edition, unknown platform, same developer/year, and conflicting provider results. Display evidence and leave separate. These weak signals cannot accumulate into an automatic merge.
5. **Incompatible** includes game vs add-on, bundle vs member, remake vs original and explicit separation. Block automatic grouping. A contradictory metadata refresh opens review and retains the last approved bindings.
6. Validate the whole proposed group. A–B and B–C edges alone do not establish A–C equivalence. Require a consistent edition anchor across every member or direct verified pairs throughout. Recompute deterministic candidates in stable key order. Bulk accept only coherent high-confidence sets.

Record merge, never-merge, keep editions separate, pin metadata, undo, split, source exclusion and never-import decisions in versioned local records. Apply changes transactionally with a revision and previous membership so undo restores group IDs. During merge choose a surviving published binding in preview; archive the other binding and ask before retiring its shortcut. During split restore archived bindings if still free. Present any collision or possible loss of Input/art associations. Never derive group IDs from mutable store preference, provider IDs, or display titles.

## Source selection

Scope publication preferences by Steam profile. Account disconnection preserves the group's identity and overrides. First apply whole-game/source exclusions, separate-output choice and explicit native-import exception. Distinguish known launchable installations from actionable install/product redirects. Exclude unusable automatic candidates (expired subscription with no independent valid action, disconnected account requiring auth, missing handler). An installed copy without ownership evidence may be launched through a verified local route with the entitlement labeled unknown. Delegate entitlement enforcement to the original launcher.

A forced store/account/product wins. If unavailable, keep the pin, display its exact problem, and default to asking before using another eligible copy. Optional per-profile fallback may use automatic selection for this launch only with a visible explanation. Never rewrite the pin. Automatic selection optionally prefers the installed-and-launchable tier first (default on). Within the chosen tier sort by the user's global order, initially GOG, Epic, Xbox, then others. Break ties by stable store/account/product keys. With installed-first disabled, priority can select a working installation redirect ahead of an installed copy. Show the resulting action before publication. Surface DRM-free capability, subscription expiry and purchase evidence without guessing either from the store's name. No viable action yields a warning, never an arbitrary URI.

## Native Steam avoidance and dispatcher

Inspect local library manifests for app IDs and install state, existing per-profile shortcuts for collision/ownership checks, and optional cached app metadata for corroborating labels. Manifests may be shared across accounts or stale. Distinguish machine presence from profile ownership; do not assert a license. Suppress a proposed duplicate when installed-native evidence and an edition-level match are reliable under the selected profile policy. Flag shared installations with uncertain profile applicability for review. Allow an explicit external-copy exception. Unowned shortcut coincidences need review; never adopt one based on title or AppID alone. A local user-supplied list of excluded Steam AppIDs (with editable scope and provenance) can cover uninstalled games without network requests. Keep this separate from detected installs. No login, tokens or profile visibility required. [Research](../research/identity.md)

Persist the publication ID in the dispatcher's validated arguments. Store selection and install state resolve when called. A change of preferred store should update application data only. Persist the initial Steam binding and publication name/target through refresh, update and restart. Different groups sharing both executable and name can collide because arguments may be ignored in ID generation. Block creation until a collision-free binding is verified. Client behavior after explicit renames, relocation, Steam Input remapping or an external launcher spawning a detached child needs client testing. [Steam contract](steam-and-artwork.md)

## Review and test oracle

Show one expandable row per publication group with edition, copy count, store and install states, active action, automatic/forced reason, and stale evidence. Offer separate rows, exclusions, merge/undo and preferred copy inline. Filter unresolved candidates. Bulk preview shows bindings being preserved or retired. Keep badges inside Sync2Steam.

The [synthetic fixtures](../testing/duplicate-fixtures.md) specify positive, negative and recovery outcomes. Assert unchanged input yields an empty second plan. Uninstall changes install state only. Offline refresh never revokes. Group operations preserve every source. Global reordering never removes a pin. Metadata changes never move an existing shortcut without review. Native titles and user-created Steam records are read-only to reconciliation.
