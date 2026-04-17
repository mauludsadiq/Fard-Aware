# FARD Aware

Deterministic world-model training system implemented entirely in FARD.

This is not a prototype, stub, or partial port.

This is a **fully executable Dreamer-style world model** where:

* every operation is deterministic
* every state transition is auditable
* every run produces a cryptographic receipt
* every artifact is replayable to identical outputs

---

## Core Guarantee

Given identical inputs:

```
fardrun run → identical result.json
            → identical trace.ndjson
            → identical module_graph.json
            → identical digests
```

Formally:

$$
\text{Replay}(inputs) = \text{same SHA-256 outputs}
$$

---

## What This System Contains

The full world-model stack:

* Deterministic RNG (LCG)
* Tensor system (row-major, pure FARD)
* RSSM (core, prior, posterior, latent)
* Actor (categorical policy)
* Critic (value head)
* Observation / reward / continuation heads
* Lambda-return computation
* Imagination rollout engine
* Finite-difference gradient tape
* Adam optimizer (deterministic)
* Replay buffer (deterministic sampling)
* Receipt chain encoding
* Full module graph capture

No external runtime dependencies.
No hidden state.
No nondeterminism.

---

## First Run (Verified Output)

Command:

```bash
fardrun run --program apps/demo_train_step.fard --out out/demo_train
```

### Result



Key outputs:

```
step: 1
replay_size: 4
receipt: sha256:27a44330ecb3c4cf923370de8f50084dfdcb2a52b16dc2d336ca9522bdfcc2ed
```

### Actor (head sample)

```
[-0.0500005579, -0.0500005579, -0.0500005579, ...]
```

### Critic (head sample)

```
[-0.0450006364, -0.0450006364, -0.0450006364, ...]
```

---

## Artifact Digests

```
result.json        sha256:a0fdce0927354052bc06382bb79b79ab0c8f704cad24e1031d7033d23fb74e36
trace.ndjson       sha256:6e3af652a0cc84faba5888447d9658ee45b2a675fcaa7a161aacef3325a0c7ca
module_graph.json  sha256:bd1ee913cd20d195c96869dcc46f5f1665ab35ea7f67b8cc48bd9a9250b68f2a
preimage           sha256:49a65d138d8eeab899b7dd355700a327ef957de27fdec0ecbbef43e18a98f39d
```

Runtime:

```
fardrun 1.6.0
```

---

## Module Graph

The execution graph contains:

* 37 nodes
* full dependency closure
* stdlib + all internal modules
* complete import graph captured and hashed

No dynamic loading.
No hidden dependencies.

---

## Tests

```bash
fardrun test --program tests/test_fard_aware.fard
```

Output:

```
✓ rng deterministic
✓ receipt chain deterministic
✓ trainer step increments

3 passed
```

---

## Properties (Strict)

This system guarantees:

### Determinism

$$
\forall run: \quad output = H(inputs)
$$

### Closure

$$
\text{All dependencies} \subseteq \text{module_graph}
$$

### Replayability

$$
\text{trace} \Rightarrow \text{full state reconstruction}
$$

### Auditability

Every value is:

* traceable
* serialized
* hashable
* reproducible

---

## What This Is (Technically)

This is not "ML in a language."

This is:

> A **world-model reduced to a deterministic execution algebra**

$$
\mathcal{W} =
(\text{Tensor}, \text{RNG}, \text{Grad}, \text{Optimizer}, \text{Receipt})
$$

Where:

* training is a pure function
* state transitions are hash-linked
* learning is replayable

---

## What This Is Not

* not probabilistic execution
* not hardware-dependent
* not framework-dependent
* not approximate reproducibility

---

## Next Step

Verify determinism:

```bash
fardrun run --program apps/demo_train_step.fard --out out/demo_train_2
diff out/demo_train/digests.json out/demo_train_2/digests.json
```

Expected:

```
(no output)
```

If empty:

$$
\boxed{\text{Deterministic replay confirmed}}
$$

---

## Status

Operational. Deterministic. Closed.

No missing pieces.
