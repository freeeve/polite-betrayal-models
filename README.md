# Polite Betrayal - Models

ONNX model artifacts for the polite-betrayal Diplomacy engine.

## Directory Structure

```
supervised/       Supervised-only baseline models (trained on human games)
rl_iter001/       First RL iteration models (populated when training completes)
current/          Current best models used by the engine at runtime
```

- **supervised/** contains the frozen supervised baselines that serve as the starting point for reinforcement learning.
- **rl_iter001/** (and subsequent `rl_iterNNN/` directories) store checkpoints from each RL self-play iteration.
- **current/** holds whichever models the engine should load. After an RL iteration improves on the baseline, copy the new models here.

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

## Models

| File | Description |
|------|-------------|
| `supervised/policy_v1.onnx` | v1 policy network (smaller, early architecture) |
| `supervised/policy_v2.onnx` | v2 policy network (supervised baseline) |
| `supervised/value_v2.onnx` | v2 value network (supervised baseline) |
| `current/policy_v2.onnx` | Current best policy (= supervised v2 until RL improves) |
| `current/value_v2.onnx` | Current best value (= supervised v2 until RL improves) |
