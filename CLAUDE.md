# Models Repository — polite-betrayal-models

## Structure

```
polite-betrayal-models/
├── CLAUDE.md
├── README.md
├── registry.json              # Index of all model versions
├── current -> models/<hash>/  # Symlink to active model
└── models/
    └── <sha256[:8]>/          # Content-addressable directory
        ├── policy_v2.onnx     # Policy network (ONNX format)
        ├── value_v2.onnx      # Value network (ONNX format)
        └── metadata.json      # Training lineage and metrics
```

## Adding a New Model

1. **Compute hash**: `shasum -a 256 policy_v2.onnx` — take first 8 chars
2. **Create directory**: `mkdir models/<hash>`
3. **Copy model files**: `cp policy_v2.onnx value_v2.onnx models/<hash>/`
4. **Create metadata.json** with training details:
   ```json
   {
     "hash": "<full sha256>",
     "created": "YYYY-MM-DD",
     "training": "supervised|rl_selfplay",
     "tag": "descriptive-tag (e.g., rl-r2-i1)",
     "round": 2,
     "iteration": 1,
     "parent": "<parent model hash[:8]>",
     "params": "15.36M",
     "best_epoch": 8,
     "best_loss": -4.3592,
     "entropy": 1.476,
     "kl": 9.1383,
     "supervised_mix": 0.3,
     "notes": "Free-text description"
   }
   ```
5. **Update registry.json**: Add entry under `models`, update `current` field
6. **Update symlink**: `rm current && ln -s models/<hash> current`
7. **Deploy to engine**: `cp models/<hash>/*.onnx ../polite-betrayal/engine/models/`
8. **Commit**: `git add -A && git commit -m "feat: add <tag> model (<hash>)"`

## Conventions

- **Hash**: First 8 chars of SHA256 of `policy_v2.onnx` (content-addressable, deterministic)
- **Tags**: `supervised-v1`, `supervised-v2`, `rl-r{round}-i{iteration}` (e.g., `rl-r2-i1`)
- **Parent**: Hash of the model this one was trained from (for lineage tracking)
- **Deduplication**: Identical ONNX files produce identical hashes — no duplicates possible
- Policy-only models (e.g., v1) omit `value_v2.onnx`

## Metadata Fields

| Field | Required | Description |
|-------|----------|-------------|
| hash | Yes | Full SHA256 of policy_v2.onnx |
| created | Yes | Date model was exported (YYYY-MM-DD) |
| training | Yes | Training method: `supervised` or `rl_selfplay` |
| tag | Yes | Human-readable identifier |
| params | Yes | Parameter count (e.g., "15.36M") |
| parent | No | Hash[:8] of parent model |
| round | No | RL self-play round number |
| iteration | No | RL iteration within round |
| best_epoch | No | Best training epoch |
| best_loss | No | Best validation loss |
| entropy | No | Policy entropy at best epoch |
| kl | No | KL divergence from reference at best epoch |
| supervised_mix | No | Fraction of supervised data mixed in (RL only) |
| notes | No | Free-text description |
