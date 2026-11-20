# Adding a store integration

The proposed first release uses built-in Rust modules registered at compile time. Each module translates one store or launcher into the shared library model. A module can expose local discovery alone. Account inventory, metadata and actions are separate capabilities. Register a driver once in the built-in registry and build the UI from the capabilities it reports. This keeps store-specific paths, APIs and protocol rules inside the adapter. See the [source review](../../.agents/research/stores.md) for examples of stores that expose different subsets.

## Integration contract

| Capability | Input and output |
|---|---|
| `InstalledDiscovery` | A discovery context with current OS user, explicitly selected launcher roots and a cancellation token. Returns observed install records and source health with no account dependency. Validate staleness, completion, directory existence, and packaged activation records as applicable. |
| `AccountInventory` | An explicit connection handle and cancellation. Returns account-scoped product/access observations with distributed or private endpoints clearly identified in the source notes. If external tooling owns authentication, expose reconnect guidance and request inventory through the supported interface. |
| `MetadataLookup` | A stable store product ID and locale. Returns optional title, edition, platform, release date, artwork references, and parent/component relationships with field provenance when available. Metadata can enrich inventory without changing identity or access. |
| `ActionResolver` | An action type, a stable key, and an optional selected install record. Returns a typed, validated intent or an unsupported/unavailable reason for this item. Resolve fresh at invocation, after confirming the user action. |

The `StoreGameId` should be an opaque ID in a stable `StoreKey` namespace. Store normalization and aliasing follow the upstream ID rules. A connection or device ID scopes observations separately from product identity. Keep app/artifact IDs, catalog IDs, offer IDs, install instance IDs, Steam AppIDs, package IDs and Windows AUMIDs distinct. Use structured `aliases` with evidence. Preserve an upstream record's platform, product kind, parent or component relationships, and access evidence. Access can be durable, claimed, subscription-based, trial, shared, time-limited, history or unknown, as appropriate to the source. A single product may have more than one access observation.

Each inventory or installation scan returns `Snapshot<T>` with records, source and scope, observation time, freshness, completeness and safe diagnostics. A successful complete empty snapshot means there are zero rows in that reported scope. A missing launcher, disconnection, expired login, invalid input, offline state, cancellation, failed page, unknown schema and ambiguous product are distinct outcomes. The coordinator retains the last known data after failed or partial refreshes and only reconciles missing records on a fresh complete snapshot of the same scope. Record the upstream adapter/tool version when it affects interpretation. Use bounded paged reads, timeouts and cancellation. A stalled or repeated cursor ends the refresh as partial.

Use narrowly typed launch and management targets. Model launch, install, open client/product, and uninstall as different action kinds. A Windows packaged launch uses a validated AUMID. A supported protocol handoff uses an adapter-owned route with typed, encoded IDs. An executable intent carries an absolute executable, an actual argument list, and a working directory if needed. Manifest strings and metadata remain untrusted and cannot become raw shell commands. The executor owns invocation policy, capability allowlists, user consent for management actions and reporting handoff separately from completion. Redact tokens, private account identifiers and paths where logs could expose personal data. Keep credentials in the app's OS-backed credential store or the delegated tool, with only opaque account handles shared with library state.

The coordinator owns joins, match confidence, cross-store grouping, sync history and planning. Provide stable source IDs and evidenced aliases. Keep candidate generation, edition matching and user decisions in the coordinator. Title similarity can suggest review only. The Steam adapter owns installed Steam detection, shortcut identity, write planning, Steam data formats and application. A new store adapter should return the same structured records and intents whether or not Steam exists on the device.

## Module template

This example sketches a fictional installed-only adapter. The types and paths are proposed names for the shared contract. It illustrates registration and responsibilities before implementation.

```rust
// src/stores/example.rs — illustrative only
use crate::stores::{
    ActionKind, ActionRequest, ActionResolver, DiscoveryContext, InstalledDiscovery,
    InstalledGame, LaunchTarget, Snapshot, StoreDriver, StoreError, StoreGameId,
    StoreKey, TypedAction,
};

pub struct ExampleStore;

impl StoreDriver for ExampleStore {
    fn key(&self) -> StoreKey {
        StoreKey::new("example")
    }

    fn installed(&self) -> Option<&dyn InstalledDiscovery> {
        Some(self)
    }

    fn actions(&self) -> Option<&dyn ActionResolver> {
        Some(self)
    }
    // Optional account and metadata capabilities default to absent.
}

impl InstalledDiscovery for ExampleStore {
    fn discover(&self, ctx: &DiscoveryContext) -> Snapshot<InstalledGame> {
        // Read known manifest roots, validate paths and report partial reads.
        // Each record uses StoreGameId::new(self.key(), product_id).
        discover_example_manifests(ctx, self.key())
    }
}

impl ActionResolver for ExampleStore {
    fn resolve(&self, request: &ActionRequest) -> Result<TypedAction, StoreError> {
        if request.kind() != ActionKind::Launch {
            return Err(StoreError::Unsupported);
        }
        let id: &StoreGameId = request.game_id();
        let install = resolve_current_example_install(id, request.install_id())?;
        Ok(TypedAction::Launch(LaunchTarget::Executable {
            path: install.executable,
            args: Vec::new(),
            working_dir: Some(install.directory),
        }))
    }
}

// In the built-in registry, add ExampleStore alongside the other drivers.
```

`discover_example_manifests` and `resolve_current_example_install` stand for the store-specific parser and live target validation. The latter must confirm that the requested key belongs to this store, that the selected installation still exists, and that the target can be constructed. `actions()` reports the presence of a resolver. The resolver still decides whether each requested action is available for the selected title.

## Contribution sequence

1. Add a research entry with pinned primary source and license, vendor API status, OS/client versions, inventory scope, identity model, and expected degraded behavior. Include a short description of what each source proves and the evidence required for any unresolved behavior. Verify terms and distribution obligations before bundling third-party executables or adapting upstream code.
2. Start with the smallest reliable capability. Put reads behind injectable filesystem, registry, HTTP or process boundaries as needed. Use explicit paths/search roots and an OS-specific adapter seam. Keep module-specific configuration and integration logic in the module. Add account inventory separately after the local path works where both are supported.
3. Add synthetic or sanitized fixtures that represent the source format and classifier. Cover repeated/changed names, edition or platform collisions, DLC or extras, missing launcher/path, corrupt records, pagination gaps and last-known-good behavior for implemented capabilities. Account fixtures include two connections, valid empty, expired authentication and account switch. Action fixtures prove rejects, escaping and exact typed targets when relevant.
4. Register the module and expose its driver/capability status through the central library command. Keep capability descriptions specific, such as `Installed titles` or `Account inventory`. Verify discovery and a preview from the normal entry point against a temporary Steam fixture and an injected executor. Report any real-client behavior that still needs manual verification.
5. Add a separate, opt-in action check when the user has the relevant client and a suitable test account. Verify outcomes from client state, including missing handler or launcher and cases where the client opens without completing the intended action. Keep such checks out of default automated tests.
