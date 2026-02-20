# Polite Betrayal - Models

ONNX model artifacts for the polite-betrayal Diplomacy engine.

## Directory Structure

```
polite-betrayal-models/
├── README.md
├── registry.json          # Model index with tags and lineage
├── current -> models/...  # Symlink to active model
└── models/
    ├── <hash>/            # Each model identified by SHA256[:8] of its policy ONNX
    │   ├── policy_v*.onnx
    │   ├── value_v*.onnx  # (if applicable)
    │   └── metadata.json
    └── ...
```

Models are stored in a **content-addressable** layout: each directory is named by the first 8 hex characters of the SHA256 hash of the policy ONNX file. This ensures deduplication and makes lineage tracking unambiguous.

## Current Models

| Hash | Tag | Training | Params | Files |
|------|-----|----------|--------|-------|
| `b883fb2e` | supervised-v1 | supervised | unknown | policy_v1.onnx |
| `cd8dfa11` | supervised-v2 | supervised | 15.36M | policy_v2.onnx, value_v2.onnx |
| `26eb8ecb` | rl-r1-i1 | rl_selfplay | 15.36M | policy_v2.onnx, value_v2.onnx |

**current** points to `26eb8ecb` (rl-r1-i1).

## Lineage

```
supervised-v1 (b883fb2e)

supervised-v2 (cd8dfa11)
  └── rl-r1-i1 (26eb8ecb)  ← current
```

## Usage

Clone this repo and symlink or copy models to `engine/models/` in the main repo:

```bash
# From the polite-betrayal root:
ln -sfn ../polite-betrayal-models/current/policy_v2.onnx engine/models/policy_v2.onnx
ln -sfn ../polite-betrayal-models/current/value_v2.onnx engine/models/value_v2.onnx
```

Or use the Makefile target in the main repo:

```bash
make models
```

## Adding a New Model

1. Compute the SHA256 of the policy ONNX file and take the first 8 hex chars.
2. Create `models/<hash>/` and copy the ONNX files in.
3. Write a `metadata.json` with hash, training type, tag, parent, and notes.
4. Add an entry to `registry.json`.
5. Update the `current` symlink if this is the new best model:
   ```bash
   rm current && ln -s models/<hash> current
   ```
