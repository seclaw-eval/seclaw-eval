# Docker Evaluation Guide

This page describes how to run the public SPECSYNTH-CLAWBENCH release with Docker.

## What Docker Runs

The public Docker backend evaluates each task in a fresh OpenClaw container. For every configured model and task, it:

1. Starts `ghcr.io/openclaw/openclaw:main` or a custom image.
2. Deploys the task fixture into the container.
3. Runs `fixture/init.sh` when present.
4. Configures the model provider from `.env` and `docker_models_config.yaml`.
5. Sends the task prompt to the agent.
6. Collects transcripts, workspace snapshots, execution metadata, grader results, and optional judge results.
7. Writes normalized batch artifacts under `batch_logs/{batch_name}`.

## Requirements

| Requirement | Notes |
|-------------|-------|
| Python | Python 3.11 or newer. |
| uv | Recommended dependency manager. The runner uses `uv run --frozen python` when `uv.lock` is present. |
| Docker | Required for isolated task execution. |
| OpenClaw image | Pulled from `ghcr.io/openclaw/openclaw:main` by default. |
| Model provider | Any OpenAI-compatible API endpoint configured through `.env`. |

## Setup

```bash
uv sync
docker pull ghcr.io/openclaw/openclaw:main

cp .env.example .env
cp docker_models_config.example.yaml docker_models_config.yaml
```

Edit `.env` with the provider values used by `docker_models_config.yaml`:

```bash
DOCKER_BACKEND_MODEL_ID=your-model-id
DOCKER_BACKEND_BASE_URL=https://api.provider.example.com/v1
DOCKER_BACKEND_API_KEY=your-model-api-key
```

`docker_models_config.yaml` supports multiple model entries. Each entry can use a different `model`, `base_url`, and `api_key_env`.

## Run The v1 Task Set

```bash
./scripts/batch_execute.sh \
  --backend docker \
  --tasks-jsonl batch_inputs/version/v1/test_tasks.jsonl \
  --models-config docker_models_config.yaml \
  --docker-concurrency 2 \
  --batch-logs batch_logs \
  --batch-name docker_eval_v1
```

To run rule-based graders only and skip LLM judge scoring:

```bash
./scripts/batch_execute.sh \
  --backend docker \
  --tasks-jsonl batch_inputs/version/v1/test_tasks.jsonl \
  --models-config docker_models_config.yaml \
  --skip-judge \
  --batch-name docker_eval_v1_grader_only
```

## Outputs

Each batch writes a reproducible result directory:

```text
batch_logs/{batch_name}/
├── jobs.jsonl
├── scores.json
├── report.md
├── analysis.json
├── logs/
├── .benchmark_runs/
└── traces/
    └── {trace_id}/
        ├── session_transcript.jsonl
        ├── transcript.jsonl
        ├── evaluation.json
        ├── grading.json
        ├── execution.json
        ├── audit_data.json
        └── workspace/
```

Use `scores.json` for aggregate results and `report.md` for a human-readable summary. Per-task debug artifacts live in `traces/{trace_id}`.

## Fixture Deployment

The Docker backend maps each task fixture to fixed container paths:

| Repository path | Container path |
|-----------------|----------------|
| `fixture/workspace/` | `/home/node/workspace/` |
| `fixture/mcp/` | `/opt/mcp/` |
| `fixture/mock_service/` | `/opt/mock_service/` |
| `fixture/local_files/` | `/opt/local_files/` |

If a task declares `workspace: /opt/workspace`, the runtime creates a compatibility symlink to `/home/node/workspace` before initialization.

## Common Options

| Option | Description |
|--------|-------------|
| `--tasks-jsonl` | JSONL task list. Recommended for released benchmark sets. |
| `--tasks-dir` | Flat task directory, used instead of `--tasks-jsonl`. |
| `--models-config` | YAML config for models under test. Required. |
| `--docker-concurrency` | Number of parallel Docker task containers per model. |
| `--docker-image` | Override the default OpenClaw runtime image. |
| `--docker-timeout` | Per-task timeout in seconds. |
| `--docker-init-timeout` | Timeout for `fixture/init.sh` in seconds. |
| `--judge-models-config` | Optional judge model config. |
| `--skip-judge` | Skip LLM judge scoring and use task graders only. |
| `--skip-analyze` | Skip generation of `report.md` and `analysis.json`. |

## Troubleshooting

| Symptom | Check |
|---------|-------|
| Docker cannot start tasks | Start Docker and confirm the runtime image can be pulled. |
| Provider returns 401 | Check the model's `api_key_env` and the matching key in `.env`. |
| Model endpoint fails | Confirm `base_url` includes the OpenAI-compatible `/v1` path when required by the provider. |
| Task setup is slow | Increase `--docker-init-timeout`. |
| MCP tools are missing | Inspect `batch_logs/{batch_name}/logs/` and per-trace stderr artifacts. |
| Judge did not run | Configure `judge_models_config.yaml` or pass `--skip-judge` intentionally. |

For implementation details, see [benchmark/README.md](../benchmark/README.md).
