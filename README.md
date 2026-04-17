# FARD Aware

A concrete FARD workspace that instantiates the Dreamer-style deterministic world-model stack as real `.fard` modules.

This package is structured for editing and running in VS Code with `fardrun` available on PATH.

## Layout

- `packages/fard_aware/src/core` — deterministic helpers (`rng`, `util`)
- `packages/fard_aware/src/math` — scalar arithmetic wrappers
- `packages/fard_aware/src/tensor` — tensor construction, indexing, reductions, softmax, matmul
- `packages/fard_aware/src/params` — exact parameter schemas and flatten/unflatten helpers
- `packages/fard_aware/src/receipt` — canonical payload encoding + digest chaining
- `packages/fard_aware/src/replay` — canonical replay buffer and deterministic sampler
- `packages/fard_aware/src/optimizer` — deterministic SGD/Adam updates with explicit optimizer state
- `packages/fard_aware/src/actor` — categorical policy, log-prob, entropy, sampling
- `packages/fard_aware/src/rssm` — deterministic core, prior, posterior, latent sampling
- `packages/fard_aware/src/heads` — observation, reward, continuation heads
- `packages/fard_aware/src/worldmodel` — ELBO-style world model losses
- `packages/fard_aware/src/returns` — exact lambda-return folds
- `packages/fard_aware/src/projections` — reward and continuation projections
- `packages/fard_aware/src/grad` — deterministic finite-difference gradient tape
- `packages/fard_aware/src/imagination` — latent rollout engine with receipt extension
- `packages/fard_aware/src/trainer` — `SystemState`, `step`, and higher-level orchestration
- `packages/fard_aware/src/schema` — exact artifact boundary schemas
- `apps` — runnable entry points
- `tests` — deterministic conformance tests

## Notes

This package is fully operational in FARD terms: no stubs, no placeholder returns, and no TODO branches.

Because current `fardrun` exposes numeric values as `Int`/`Float` rather than canonical raw float bytes, the package uses a **runtime-operational canonicalization layer**:

- scalars are represented as FARD `Float`
- tensors are records `{ shape, data }` with row-major `List(Float)` data
- digests and receipts are canonicalized through `json.canonicalize` + `hash.sha256_text`
- gradients are computed deterministically via finite differences over explicit flattened parameter vectors

That makes the package executable today while preserving the contract boundaries needed for a stricter byte-level kernel later.

## Run

### Demo train step

```bash
fardrun run --program apps/demo_train_step.fard --out out/demo_train
```

### Tests

```bash
fardrun test --program tests/test_fard_aware.fard
```

## Primary artifacts

The demo app writes `out/fard_aware_demo_summary.json` and prints a deterministic state summary.
