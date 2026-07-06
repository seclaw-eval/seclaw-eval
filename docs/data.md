# Dataset Guide

This page describes the public SPECSYNTH-CLAWBENCH task dataset included in this repository.

## Release Summary

| Item | Value |
|------|-------|
| Dataset target | `openclaw` |
| Release list | `batch_inputs/task_list.jsonl` |
| Number of tasks | 150 |
| Task directories | `tasks/openclaw/` |
| Primary runner | `scripts/batch_execute.sh --backend docker` |

The release uses a JSONL task list so experiments can refer to a stable task set while the repository keeps full task fixtures under `tasks/openclaw/`.

## Directory Layout

```text
batch_inputs/
└── task_list.jsonl

tasks/
└── openclaw/
    └── <task_id>/
        ├── task.yaml
        ├── metadata.yaml
        ├── grader.py
        └── fixture/
            ├── init.sh
            ├── workspace/
            ├── mcp/
            ├── mock_service/
            └── local_files/
```

Not every task needs every optional fixture subdirectory, but the runtime understands the layout above.

## Task List Format

Each line in `batch_inputs/task_list.jsonl` is a JSON object:

```json
{"task_name": "example task name", "task_id": "task_safety_example_00000000", "target": "openclaw"}
```

| Field | Meaning |
|-------|---------|
| `task_name` | Human-readable task label. |
| `task_id` | Directory name under `tasks/{target}/`. |
| `target` | Dataset namespace. The public release uses `openclaw`. |

The loader resolves each row to:

```text
tasks/{target}/{task_id}
```

## Task Directory Files

| File or directory | Purpose |
|-------------------|---------|
| `task.yaml` | Task prompt and runtime declarations. |
| `metadata.yaml` | Safety category, source metadata, and task descriptors. |
| `grader.py` | Task-specific grader used after agent execution. |
| `fixture/workspace/` | Files made available to the agent workspace. |
| `fixture/mcp/` | MCP tool servers exposed during the task. |
| `fixture/mock_service/` | Local mock services used by MCP tools or the workspace. |
| `fixture/local_files/` | Additional local assets copied into the container. |
| `fixture/init.sh` | Optional initialization script executed before the agent run. |

## Using The Dataset

Run the full public task list:

```bash
./scripts/batch_execute.sh \
  --backend docker \
  --tasks-jsonl batch_inputs/task_list.jsonl \
  --models-config docker_models_config.yaml \
  --batch-name docker_eval
```

Run from a task directory instead of the JSONL release list:

```bash
./scripts/batch_execute.sh \
  --backend docker \
  --tasks-dir tasks/openclaw \
  --models-config docker_models_config.yaml \
  --batch-name docker_eval_openclaw
```

The JSONL path is recommended for published comparisons because it pins the exact release list.

## Extending Tasks

New tasks should follow the same directory contract:

```text
tasks/openclaw/<new_task_id>/
├── task.yaml
├── metadata.yaml
├── grader.py
└── fixture/
```

Add new release rows to a versioned JSONL file under `batch_inputs/version/` when publishing a stable benchmark split.

For authoring details, see [Task Extension Guide](task-extension-guide.md) and [Grading Design](grading-design.md).
