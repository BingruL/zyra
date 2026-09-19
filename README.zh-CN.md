# Zyra

[English](README.md) | [简体中文](README.zh-CN.md)

Zyra 是面向长程复杂任务的动态异构多智能体运行时。仓库包含任务 API、TypeScript CLI、代码执行工作进程、Web 工作台，以及运行时、记忆、工具和工作区等共享组件。

## 主要组件

- `apps/api`：Python API 和控制平面。
- `apps/cli`：基于 Bun 和 TypeScript 的命令行客户端。
- `apps/code-worker`：代码工作进程入口。
- `apps/web`：React Web 工作台。
- `packages/core`：共享类型、协议和基础设施。
- `packages/runtime`：运行时、事件和 provider 控制组件。
- `packages/memory`：记忆、检索和压缩组件。
- `packages/integrations`：外部运行时和 provider 集成。
- `packages/workers`：异构 worker 适配器。
- `scripts`：开发启动、构建、检查和演示脚本。
- `tests`：Python、TypeScript 和集成测试。
- `config`：运行配置和策略文件。

## 环境要求

- Python 3.12 或更高版本
- Bun 1.2.15
- Windows PowerShell（下面的命令按 PowerShell 编写）

真实 provider 的密钥只应保存在本地环境文件中，不要提交到 Git。

## 安装

在仓库根目录执行：

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -e ".[test]"
bun install
Copy-Item .env.example .env
```

根据需要编辑 `.env`，填写 provider 密钥和本地端口。默认 API 地址是 `http://127.0.0.1:8000`，默认 Web 地址是 `http://127.0.0.1:5173`。

## 启动开发服务

先构建 Web 工作台，再启动 API 和 Web：

```powershell
bun run build:web
.\.venv\Scripts\python.exe scripts\dev_up.py
```

`dev_up.py` 会等待 API 健康检查通过后再启动 Web，按 `Ctrl+C` 会同时停止两个服务。

如果需要分别启动，可以使用：

```powershell
.\.venv\Scripts\python.exe scripts\dev_api.py
$env:ZYRA_WEB_API_ORIGIN = "http://127.0.0.1:8000"
.\.venv\Scripts\python.exe scripts\dev_web.py
```

## CLI

查看命令帮助：

```powershell
bun apps/cli/src/index.ts --help
```

常用命令：

```powershell
bun apps/cli/src/index.ts run "执行一个任务"
bun apps/cli/src/index.ts ls
bun apps/cli/src/index.ts doctor
bun apps/cli/src/index.ts daemon status
bun apps/cli/src/index.ts ui
```

CLI 默认连接本地 API，也可以通过 `--base-url` 或 `ZYRA_API_URL` 指定其他地址。

## 测试和检查

```powershell
bun run typecheck
bun run test:cli
bun run test:web
bun run runtime:test
.\.venv\Scripts\python.exe -m pytest
```

针对单个组件时，优先使用 `package.json` 中对应的 `typecheck:*` 和 `test:*` 脚本。

## 配置和运行数据

`.env.example` 列出了本地开发所需的环境变量。默认运行数据写入 `tmp/`，包括事件日志、SQLite 数据库、权限状态、工作区和 artifacts；这些运行数据不属于源代码。
