# Zyra

[English](README.md) | [简体中文](README.zh-CN.md)

Zyra is a dynamic heterogeneous multi-agent runtime for long-horizon complex tasks. The repository includes a task API, a TypeScript CLI, a code execution worker, a Web workbench, and shared runtime, memory, tool, and workspace components.

## Components

- `apps/api`: Python API and control plane.
- `apps/cli`: Bun and TypeScript command-line client.
- `apps/code-worker`: Code execution worker entry point.
- `apps/web`: React Web workbench.
- `packages/core`: Shared types, protocols, and infrastructure.
- `packages/runtime`: Runtime, event, and provider-control components.
- `packages/memory`: Memory, retrieval, and compression components.
- `packages/integrations`: External runtime and provider integrations.
- `packages/workers`: Heterogeneous worker adapters.
- `scripts`: Development, build, validation, and demo scripts.
- `tests`: Python, TypeScript, and integration tests.
- `config`: Runtime configuration and policy files.

## Requirements

- Python 3.12 or newer
- Bun 1.2.15
- Windows PowerShell for the commands below

Keep provider credentials in local environment files. Never commit real secrets to Git.

## Installation

Run these commands from the repository root:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -e ".[test]"
bun install
Copy-Item .env.example .env
```

Edit `.env` as needed for provider credentials and local ports. The default API address is `http://127.0.0.1:8000`, and the default Web address is `http://127.0.0.1:5173`.

## Start the development services

Build the Web workbench, then start the API and Web services:

```powershell
bun run build:web
.\.venv\Scripts\python.exe scripts\dev_up.py
```

`dev_up.py` waits for the API health check before starting the Web service. Press `Ctrl+C` to stop both services.

To start them separately:

```powershell
.\.venv\Scripts\python.exe scripts\dev_api.py
$env:ZYRA_WEB_API_ORIGIN = "http://127.0.0.1:8000"
.\.venv\Scripts\python.exe scripts\dev_web.py
```

## CLI

Show the command help:

```powershell
bun apps/cli/src/index.ts --help
```

Common commands:

```powershell
bun apps/cli/src/index.ts run "Run a task"
bun apps/cli/src/index.ts ls
bun apps/cli/src/index.ts doctor
bun apps/cli/src/index.ts daemon status
bun apps/cli/src/index.ts ui
```

The CLI connects to the local API by default. Use `--base-url` or `ZYRA_API_URL` to select another API endpoint.

## Tests and checks

```powershell
bun run typecheck
bun run test:cli
bun run test:web
bun run runtime:test
.\.venv\Scripts\python.exe -m pytest
```

For a single component, prefer the corresponding `typecheck:*` and `test:*` scripts in `package.json`.

## Configuration and runtime data

`.env.example` lists the environment variables used for local development. Runtime data is written to `tmp/`, including event logs, SQLite databases, permission state, workspace state, and artifacts. These files are runtime output rather than source code.
