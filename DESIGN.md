# Code Environment Toolkit - MVP 技术设计方案

## 1. 项目定位

### 1.1 项目目标

`code-env-toolkit` 是一个跨平台开发环境编排工具，用于在 Windows、macOS、Linux 上以尽量一致的方式安装、配置、验证并维护开发工具链。

V1 的目标不是覆盖所有语言生态，而是先把以下三类能力做稳：

1. **基础工具层**
   - Git
   - 终端增强工具
   - 编辑器
2. **前端开发环境**
   - FNM
   - Node.js
   - PNPM
   - npm/pnpm registry 配置
3. **Go 开发环境**
   - Go 运行时
   - GOPROXY / GOSUMDB / GOPRIVATE 配置
   - 常用 Go 工具链（如 `gopls`、`dlv`）

### 1.2 核心原则

- **三大系统一等支持**：Windows、macOS、Linux 都是 V1 正式支持目标。
- **用户态优先**：默认安装到当前用户目录，尽量不写系统目录、不要求管理员权限。
- **编排优先于脚本堆叠**：核心价值是统一工具模型、安装计划、平台执行，而不是堆很多独立脚本。
- **可验证、可恢复**：每一步安装都要有预检、结果校验和失败恢复点。
- **最小必要功能**：V1 不追求“大而全”，优先把基础工具、前端、Go 跑通并稳定。

### 1.3 非目标

以下内容不纳入 V1 的正式执行范围：

- Java、Python、Rust、.NET、数据库、Docker 等其他生态的完整安装
- GUI 图形界面
- 完整插件市场
- 离线安装包全量镜像体系

V1 只为后续扩展预留接口，不在本阶段实现完整能力。

---

## 2. 总体方案

### 2.1 设计结论

V1 采用 **“工具目录 + 安装计划器 + 平台适配器”** 架构。

这意味着系统中有三种不同职责的层：

1. **工具目录（Catalog）**
   - 描述“装什么”
   - 定义工具元数据、依赖、配置项、支持平台、校验方式
2. **安装计划器（Planner）**
   - 描述“按什么顺序装”
   - 根据用户选择和当前机器状态生成可执行任务图
3. **平台适配器（Platform Adapters）**
   - 描述“在当前系统上怎么装”
   - 针对 Windows、macOS、Linux 使用不同安装策略

### 2.2 架构分层

```text
┌──────────────────────────────────────────────┐
│ Bootstrap Layer                              │
│ 下载并安装自包含 launcher，确保工具可启动     │
└──────────────────────────────────────────────┘
                     ↓
┌──────────────────────────────────────────────┐
│ CLI / Application Core                       │
│ 参数解析、交互、任务编排、状态管理            │
└──────────────────────────────────────────────┘
                     ↓
┌──────────────────────────────────────────────┐
│ Catalog + Planner                            │
│ 工具目录、依赖解析、安装计划、冲突检测        │
└──────────────────────────────────────────────┘
                     ↓
┌──────────────────────────────────────────────┐
│ Platform Adapters                            │
│ Windows / macOS / Linux 安装执行策略         │
└──────────────────────────────────────────────┘
                     ↓
┌──────────────────────────────────────────────┐
│ Validator + Doctor + State Store             │
│ 预检、安装后校验、诊断、结果持久化            │
└──────────────────────────────────────────────┘
```

---

## 3. 发布形态与启动方式

### 3.1 启动形态

### 3.2 自包含 Launcher

发布产物不是先发 npm 包给用户执行，而是先发每个平台的 **自包含 launcher**：

- Windows: `env-toolkit.exe` + `install.ps1`
- macOS: `env-toolkit` + `install.sh`
- Linux: `env-toolkit` + `install.sh`

主程序仍然使用 TypeScript 开发，但发布时会打包成每个平台可直接运行的 launcher。这样做有两个好处：

1. **Bootstrap 只解决“拉起工具”**
2. **Node.js 是否安装，不影响 env-toolkit 自己运行**

> **架构选型优化建议：**
> 考虑到传统的 Node.js `pkg` 已停止维护，且 Node.js 官方 SEA 打包体积过大（影响弱网下载），建议使用 `Bun compile` 或 `Deno compile` 进行 TypeScript 到单文件的编译，或者将“Bootstrap 下载器 + 预检”这层用 Rust/Go 重写以保持极致的轻量级（几 MB）。

### 3.3 Bootstrap 责任边界

Bootstrap 只负责：

- 检测系统和架构
- 下载对应平台 launcher
- 校验 SHA256
- 安装到用户态 bin 目录
- 追加用户 PATH
- 验证 `env-toolkit --version`

Bootstrap **不负责**：

- 安装全部开发环境
- 直接修改 npm / Go / Git 等配置
- 进行复杂交互式安装决策

复杂逻辑全部进入主程序统一处理。

### 3.4 用户态目录约定

V1 统一采用用户目录安装：

| 平台 | 数据根目录 | 可执行目录 |
|------|------------|------------|
| Windows | `%LOCALAPPDATA%\\code-env-toolkit` | `%USERPROFILE%\\.local\\bin` |
| macOS | `~/Library/Application Support/code-env-toolkit` | `~/.local/bin` |
| Linux | `${XDG_DATA_HOME:-~/.local/share}/code-env-toolkit` | `~/.local/bin` |

要求：

- 只做 **追加式** PATH 处理
- 不覆盖系统级 PATH
- 不把 Machine PATH 重新写入 User PATH
- shell 配置修改必须幂等：**采用集中式入口文件 (Hook) 策略**。Toolkit 在用户的 Profile（如 `.zshrc` / `.bashrc`）中只注入唯一的一行（如 `[ -s "$HOME/.local/share/code-env-toolkit/env.sh" ] && source "$HOME/.local/share/code-env-toolkit/env.sh"`），所有的 PATH 追加、工具初始化命令等全部由 Toolkit 动态写入自己管理的 `env.sh` 中。

---

## 4. V1 支持范围

### 4.1 Profile 设计

V1 以 profile 为安装入口，而不是让用户在几十个工具里自由拼装。

预置 profile：

1. `base`
   - Git
   - 终端增强工具
   - 编辑器
2. `frontend`
   - FNM
   - Node.js
   - PNPM
   - registry 配置
3. `go`
   - Go
   - Go 环境变量
   - Go 常用工具

用户可以组合执行：

```bash
env-toolkit install base
env-toolkit install frontend
env-toolkit install go
env-toolkit install base frontend go
```

### 4.2 工具清单

#### `base` profile

| 工具 ID | 类型 | 默认策略 | 说明 |
|--------|------|----------|------|
| `git` | 必装 | 用户态优先 | 版本控制基础 |
| `terminal` | 默认安装 | 平台适配 | 终端增强工具，允许跳过 |
| `editor` | 默认安装 | 用户态优先 | 默认采用 VS Code，允许跳过 |
| `shell-setup` | 必装 | 配置型任务 | PATH、代理、profile 初始化 |

#### `frontend` profile

| 工具 ID | 类型 | 默认策略 | 说明 |
|--------|------|----------|------|
| `fnm` | 必装 | 用户态安装 | Node.js 版本管理 |
| `node` | 必装 | 通过 FNM 管理 | 默认安装推荐 LTS |
| `pnpm` | 必装 | Node 工具安装 | 包管理器 |
| `npm-registry` | 推荐 | 配置型任务 | 配置 npm/pnpm registry |

#### `go` profile

| 工具 ID | 类型 | 默认策略 | 说明 |
|--------|------|----------|------|
| `go` | 必装 | 官方发行版用户态安装 | Go 运行时 |
| `go-env` | 必装 | 配置型任务 | GOPROXY / GOSUMDB / GOPRIVATE |
| `go-tools` | 默认安装 | `go install` | `gopls`、`dlv` 等，允许跳过 |

### 4.3 核心依赖约束

V1 的核心依赖约束如下：

- npm/pnpm 源配置通过 toolkit 自身的配置任务完成，不以 NRM 作为核心依赖
- Go 运行时通过官方发行版进行用户态安装和管理，不以 GVM 作为核心依赖

如果后续需要扩展生态工具，可将 NRM、GVM 等能力作为可选工具接入 Catalog。

---

## 5. 工具目录设计

### 5.1 工具元数据结构

```typescript
type Platform = 'windows' | 'macos' | 'linux';

interface ToolDefinition {
  id: string;
  displayName: string;
  category: 'base' | 'frontend' | 'go';
  requiredByDefault: boolean;
  dependencies: string[];
  configurable: boolean;
  validations: ValidationSpec[];
  strategies: Partial<Record<Platform, PlatformStrategy>>;
}

interface PlatformStrategy {
  mode: 'portable' | 'user-installer' | 'package-manager' | 'config-only';
  privilege: 'user' | 'optional-elevated' | 'requires-elevated';
  source: ArtifactSource;
  installRootTemplate?: string;
  binPaths?: string[];
  envChanges?: EnvChange[];
  fallback?: FallbackStrategy[];
}
```

### 5.2 Artifact Source

所有下载型工具都必须通过统一 artifact manifest 管理：

```json
{
  "toolId": "git",
  "platform": "windows",
  "version": "2.49.0",
  "url": "https://...",
  "sha256": "xxxx",
  "archiveType": "zip",
  "entrypoints": ["cmd/git.exe"]
}
```

> **版本解析器 (Version Resolver) 设计：**
> 为了支持诸如 `versionPolicy: "lts"` 这样的动态需求，在 Planner 阶段前需引入 Version Resolver。可考虑在云端维护一个动态更新的 `manifest-index.json`（映射 `lts` -> 具体版本及 sha256）。执行时先拉取最新 manifest 索引，再根据解析出的精确版本号执行下载和校验。

这样做的目的：

- 下载地址和业务逻辑分离
- 便于版本升级
- 便于做校验和回滚
- 便于测试环境替换下载源

### 5.3 平台策略类型

V1 统一支持四类策略：

1. **portable**
   - 下载压缩包并解压到用户目录
   - 适合 FNM、Go、部分终端工具
2. **user-installer**
   - 执行支持用户态安装的官方安装器
   - 适合 Git、VS Code 等
3. **package-manager**
   - 使用系统包管理器或平台官方商店
   - 只作为回退或显式选择策略
4. **config-only**
   - 不下载软件，只写配置和环境变量
   - 适合 registry、GOPROXY、shell 配置

---

## 6. 安装计划器设计

### 6.1 计划器职责

安装计划器是 V1 的核心模块，负责把“用户想装什么”转换成“系统现在应该做什么”。

输入：

- 用户选择的 profiles / tools
- 当前平台、架构、shell、包管理器能力
- 已安装工具状态
- 配置项
- 代理和网络情况

输出：

- 有依赖顺序的任务图
- 每个任务的执行策略
- 每个任务需要的权限级别
- 变更预览

### 6.2 状态分类

每个工具在计划阶段会被归类为以下状态之一：

- `not-installed`
- `installed-unmanaged`
- `installed-managed`
- `needs-config-update`
- `version-conflict`
- `unsupported`

> **非托管工具 (Unmanaged) 的冲突处理：**
> 针对 `installed-unmanaged` 状态（如用户已通过 Homebrew 安装的工具），需要在 ToolDefinition 或全局配置中提供 `conflictResolution` 策略选项（例如 `skip` 保留现状，或 `shadow` 通过 PATH 优先级覆盖）。在执行前预览中，必须明确提示用户处理方式（如：“检测到系统级 Git，将通过覆盖 PATH 优先使用 Toolkit 版本”）。

示例：

- 系统已有 Git，但不是 toolkit 安装的，记为 `installed-unmanaged`
- Go 已装但 GOPROXY 不符，记为 `needs-config-update`
- 已安装的 Node 主版本与目标不一致，记为 `version-conflict`

### 6.3 任务类型

```typescript
type PlannedTask =
  | 'download-artifact'
  | 'verify-checksum'
  | 'extract-artifact'
  | 'run-installer'
  | 'write-config'
  | 'append-path'
  | 'validate-command'
  | 'record-state'
  | 'cleanup-temp';
```

### 6.4 依赖顺序

V1 默认顺序如下：

#### `base`

```text
shell-setup
  -> git
  -> terminal
  -> editor
```

#### `frontend`

```text
fnm
  -> node
  -> pnpm
  -> npm-registry
```

#### `go`

```text
go
  -> go-env
  -> go-tools
```

如果多个 profile 一起安装，计划器只保留必要任务并去重。

### 6.5 执行前预览

在真正执行前，CLI 必须输出安装预览：

- 将安装哪些工具
- 使用什么策略
- 写入哪些目录
- 是否需要提权
- 将修改哪些 shell/profile 文件

用户确认后再执行。

---

## 7. 平台适配器设计

### 7.1 统一接口

```typescript
interface PlatformAdapter {
  detect(): Promise<SystemSnapshot>;
  prepare(task: ExecutionTask): Promise<PreparedTask>;
  execute(task: PreparedTask): Promise<TaskResult>;
  validate(task: PreparedTask): Promise<ValidationResult>;
}
```

### 7.2 Windows 适配器

默认策略：

- Shell: PowerShell
- 用户目录：`%LOCALAPPDATA%`、`%USERPROFILE%\\.local\\bin`
- 用户态优先下载 zip / user installer
- 回退策略：必要时使用 `winget`

重点处理：

- PowerShell profile 修改幂等化
- User PATH 追加式写入
- 安装器静默参数统一
- VS Code / Git 用户安装模式识别

### 7.3 macOS 适配器

默认策略：

- Shell: zsh
- 用户目录：`~/Library/Application Support`、`~/.local/bin`
- 用户态优先下载 tar.gz / app bundle
- 回退策略：必要时使用 Homebrew

重点处理：

- `~/.zprofile` / `~/.zshrc` 的 PATH 写入 (通过集中式 `env.sh` Hook)
- `.app` 安装到 `~/Applications`
- **命令行软链**：对于 VS Code 等编辑器，安装 `.app` 后，必须额外创建软链接或配置 PATH（例如将 `code` 软链到 `~/.local/bin/code`），确保命令行调用畅通。
- Gatekeeper / quarantine 的兼容处理

### 7.4 Linux 适配器

默认策略：

- Shell: bash 或 zsh
- 用户目录：XDG 路径 + `~/.local/bin`
- 用户态优先下载 tar.gz / AppImage / 官方归档包
- 回退策略：必要时使用 `apt` / `dnf` / `yum` / `pacman`

重点处理：

- 发行版差异屏蔽
- shell profile 写入
- Desktop 环境不一致导致的 GUI 工具安装差异

### 7.5 终端和编辑器策略

由于 GUI 工具跨平台差异大，V1 采用“统一类别、平台选型”的方式：

- `editor`
  - 默认产品：VS Code
  - Windows/macOS/Linux 都支持
- `terminal`
  - Windows：优先 Windows Terminal + PowerShell 7
  - macOS/Linux：优先 WezTerm，若不适配则跳过终端安装，只保留 shell 配置

这部分必须在文档和 CLI 里明确显示为“平台推荐实现”，而不是假装三端完全同构。

---

## 8. 执行器设计

### 8.1 执行原则

- **严格遵守依赖拓扑顺序**：计划器 (Planner) 生成的任务图（DAG）是有向无环图，执行器必须严格按照依赖顺序**串行**执行有强依赖关系的任务（例如：必须先装 fnm 才能装 node，必须先装 node 才能装 pnpm）。
- **无依赖任务的并发执行**：在拓扑层级相同的无依赖任务（例如，下载 git 的 artifact 和下载 fnm 的 artifact 可以同时进行），推荐使用**并行**（Promise.all / worker）以加快整体安装速度。但为了日志可读性，需要提供良好的进度和并发控制（如最大并发数限制）。
- 每一步都写入 checkpoint
- 校验失败立即中断，并输出恢复建议

### 8.2 Checkpoint 模型

```json
{
  "runId": "2026-04-03T10:00:00Z",
  "toolId": "go",
  "task": "extract-artifact",
  "status": "completed",
  "installRoot": "/home/user/.local/share/code-env-toolkit/go/1.22.0",
  "artifacts": ["go1.22.0.linux-amd64.tar.gz"]
}
```

作用：

- 失败后支持继续执行
- 支持定位半安装状态
- 支持 `doctor` 和 `repair`

### 8.3 回滚策略

V1 不做复杂事务型全量回滚，但需要保证：

- 未完成安装不会被错误标记为成功
- 临时文件会清理
- 可执行入口只在校验通过后暴露到 PATH
- 已写入配置会被记录来源

### 8.4 执行结果输出规范

每次安装执行完成后，CLI 必须：

1. 在终端输出一份**人可读摘要**
2. 在状态目录中**强制写入结果文件**
3. 在需要时额外导出到用户指定路径

输出目标：

- 让用户立即知道这次安装成功了什么、失败了什么
- 让用户准确看到安装目录、PATH、环境变量、配置文件改动
- 让后续 `status`、`doctor`、`repair` 能直接复用执行结果

### 8.5 终端完成摘要

默认终端输出必须至少包含以下信息：

1. 执行概览
   - `runId`
   - 开始时间 / 结束时间 / 总耗时
   - 当前平台、架构、shell
   - 本次选择的 profiles
2. 结果统计
   - 成功数量
   - 跳过数量
   - 复用已有安装数量
   - 失败数量
3. 工具级结果
   - 工具名称
   - 状态：`installed` / `updated` / `reused` / `skipped` / `failed`
   - 实际版本
   - 安装目录
4. 关键环境变更
   - 新增到 PATH 的目录
   - 修改过的 shell/profile 文件
   - 写入的环境变量
5. 下一步提示
   - 是否需要重开终端
   - 是否需要重新加载 shell profile
   - 常用验证命令

示例：

```text
Install completed
Run ID: 2026-04-03T10:00:00Z
Platform: macOS arm64
Profiles: base, frontend, go
Duration: 4m12s

Results
- installed: 6
- reused: 2
- skipped: 1
- failed: 0

Tools
- git: installed 2.49.0
  install root: /Users/jan/.local/share/code-env-toolkit/tools/git/2.49.0
  bin paths: /Users/jan/.local/bin
- node: installed 22.3.0
  install root: /Users/jan/.local/share/code-env-toolkit/tools/node/22.3.0
  managed by: fnm
- go: installed 1.22.0
  install root: /Users/jan/.local/share/code-env-toolkit/tools/go/1.22.0

PATH changes
- appended: /Users/jan/.local/bin

Shell changes
- updated: /Users/jan/.zprofile

Environment changes
- GOPROXY=https://goproxy.cn,direct
- GOSUMDB=sum.golang.org

Validation
- git --version: passed
- node --version: passed
- pnpm --version: passed
- go version: passed

Next steps
- run: source ~/.zprofile
- run: env-toolkit doctor
```

### 8.6 结构化详细报告

每次执行后都必须在状态目录下写入结果文件，结果文件属于**强制产物**，不允许因为未传 `--report-file` 而省略。

| 平台 | 报告目录 |
|------|----------|
| Windows | `%LOCALAPPDATA%\\code-env-toolkit\\runs` |
| macOS | `~/Library/Application Support/code-env-toolkit/runs` |
| Linux | `${XDG_DATA_HOME:-~/.local/share}/code-env-toolkit/runs` |

默认文件命名：

- `<runId>.json`
- `<runId>.txt`

要求：

- `.json` 为机器可读主报告
- `.txt` 为人工阅读摘要报告
- 两个文件都必须生成
- 写文件失败时，本次执行不能标记为完整成功，必须给出明确报错
- 如果用户传入 `--report-file`，则在默认结果文件之外，再额外导出一份到指定位置

JSON 报告字段要求：

```json
{
  "runId": "2026-04-03T10:00:00Z",
  "status": "completed",
  "platform": {
    "os": "linux",
    "arch": "x64",
    "shell": "bash"
  },
  "profiles": ["base", "frontend", "go"],
  "summary": {
    "installed": 6,
    "updated": 1,
    "reused": 2,
    "skipped": 1,
    "failed": 0,
    "durationMs": 252000
  },
  "tools": [
    {
      "toolId": "go",
      "status": "installed",
      "version": "1.22.0",
      "installRoot": "/home/jan/.local/share/code-env-toolkit/tools/go/1.22.0",
      "binPaths": ["/home/jan/.local/bin"],
      "managedByToolkit": true,
      "artifacts": [
        {
          "url": "https://...",
          "sha256": "..."
        }
      ],
      "envChanges": [
        { "name": "GOPROXY", "value": "https://goproxy.cn,direct" }
      ],
      "pathChanges": [
        { "action": "append", "value": "/home/jan/.local/bin" }
      ],
      "fileChanges": [
        {
          "path": "/home/jan/.bashrc",
          "action": "updated"
        }
      ],
      "validations": [
        {
          "command": "go version",
          "success": true,
          "stdout": "go version go1.22.0 linux/amd64",
          "stderr": "",
          "exitCode": 0
        }
      ]
    }
  ]
}
```

### 8.7 详细报告内容边界

详细报告必须覆盖以下内容，不得只保留“安装成功/失败”这种粗粒度信息：

- 本次执行的 profiles 和 tools
- 每个工具的最终状态
- 实际安装版本
- 安装根目录
- 二进制目录和 launcher 暴露路径
- 新增、更新、跳过、复用、失败的具体原因
- 下载源、镜像源、checksum 校验结果
- 写入或修改的配置文件路径
- 修改过的 shell/profile 文件路径
- PATH 的新增项
- 设置或更新的环境变量
- 每个验证命令的 stdout/stderr 和退出码
- 每个步骤耗时
- 失败步骤的错误分类与恢复建议

### 8.8 CLI 输出模式

CLI 至少支持三种输出模式：

1. 默认模式
   - 输出精简但完整的完成摘要
2. `--verbose`
   - 输出逐步骤详情和每个任务的目标路径
3. `--json`
   - 直接将摘要输出为结构化 JSON，便于 CI 或外部系统消费

并支持：

```bash
env-toolkit install base frontend go --json
env-toolkit install frontend --verbose
env-toolkit install go --report-file ./install-report.json
```

说明：

- `--json` 控制终端 stdout 的输出格式，不影响默认结果文件落盘
- `--verbose` 控制终端展示粒度，不影响默认结果文件落盘
- `--report-file` 用于将本次结果额外导出到指定文件，不替代默认结果文件

### 8.9 与 `status` / `doctor` 的关系

- `status` 必须可以读取最近一次成功执行结果，并展示各工具的安装目录、版本、PATH 暴露状态
- `doctor` 必须把“当前状态”和“最近一次执行结果”进行对比，指出漂移项
- `repair` 必须能基于最近一次执行报告，重放 PATH、环境变量和配置修复步骤

---

## 9. 配置与状态持久化

### 9.1 配置文件位置

| 平台 | 配置目录 |
|------|----------|
| Windows | `%APPDATA%\\code-env-toolkit` |
| macOS | `~/Library/Application Support/code-env-toolkit` |
| Linux | `${XDG_CONFIG_HOME:-~/.config}/code-env-toolkit` |

### 9.2 配置文件结构

```json
{
  "version": "1",
  "profiles": {
    "base": {
      "enabled": true
    },
    "frontend": {
      "node": {
        "versionPolicy": "lts",
        "preferredMajor": 22
      },
      "registry": {
        "npm": "https://registry.npmmirror.com"
      }
    },
    "go": {
      "version": "1.22.0",
      "env": {
        "GOPROXY": "https://goproxy.cn,direct",
        "GOSUMDB": "sum.golang.org",
        "GOPRIVATE": ""
      },
      "tools": ["gopls", "dlv"]
    }
  },
  "network": {
    "proxy": {
      "http": "",
      "https": ""
    },
    "downloadMirror": ""
  }
}
```

### 9.3 状态文件结构

```json
{
  "installedTools": {
    "git": {
      "managed": true,
      "version": "2.49.0",
      "installRoot": "...",
      "binPaths": ["..."],
      "lastValidatedAt": "2026-04-03T10:00:00Z"
    }
  }
}
```

配置和状态必须分离：

- **配置** 表示用户想要什么
- **状态** 表示系统现在是什么

### 9.4 运行历史

除当前状态外，系统还需要维护最近执行记录索引，用于 `status`、`doctor`、`repair` 快速定位：

```json
{
  "lastRunId": "2026-04-03T10:00:00Z",
  "lastSuccessfulRunId": "2026-04-03T10:00:00Z",
  "recentRuns": [
    "2026-04-03T10:00:00Z",
    "2026-04-02T09:12:10Z"
  ]
}
```

---

## 10. 代理、网络与安全

### 10.1 代理处理原则

代理配置必须在预检阶段就生效，而不是下载失败后再补救。

> **工具执行时的代理注入：**
> 明确 toolkit `network.proxy` 的作用范围。建议在 `base` profile 中增加一个隐式任务：如果 toolkit 配置了全局代理，自动帮用户设置常规的 `HTTP_PROXY` 环境变量，甚至针对 Git / npm 等工具自动设置对应的代理配置（如 `git config --global http.proxy`）。

优先级：

1. CLI 显式参数
2. 配置文件
3. 环境变量
4. 系统默认

支持：

- HTTP / HTTPS 代理
- 不同平台下载器统一透传
- 对命令执行阶段的环境变量注入

### 10.2 网络预检

不能再使用单一的 `ping github.com` 作为联网判断。

V1 采用：

- DNS 解析检查
- 目标源 HEAD/GET 探测
- 代理连通性检查
- 下载源可用性检查

并给出精确错误分类：

- DNS 失败
- TLS 失败
- 代理认证失败
- 超时
- 源站不可达

### 10.3 安全要求

所有外部下载必须：

- 通过 artifact manifest 声明
- 校验 SHA256
- 记录下载来源和版本
- 明确是否为官方源或镜像源

V1 不允许“下载后直接执行且无校验”的实现。

---

## 11. 校验、诊断与修复

### 11.1 安装后校验

每个工具必须定义校验命令，例如：

| 工具 | 校验命令 |
|------|----------|
| Git | `git --version` |
| FNM | `fnm --version` |
| Node.js | `node --version` |
| PNPM | `pnpm --version` |
| Go | `go version` |
| Go tools | `gopls version` / `dlv version` |
| VS Code | `code --version` |

### 11.2 `doctor` 命令

`env-toolkit doctor` 用于输出诊断报告：

- PATH 是否完整
- 所需命令是否可执行
- 配置文件是否冲突
- shell profile 是否已生效
- 代理是否可用
- 已安装工具是否被外部改动

### 11.3 `repair` 命令

V1 增加基础修复能力：

- 重新追加 PATH
- 重写 shell 配置段
- 重新执行某个工具的校验和配置步骤
- 重新下载损坏 artifact

---

## 12. CLI 设计

### 12.1 命令列表

```bash
env-toolkit init
env-toolkit install [profile...]
env-toolkit list
env-toolkit status
env-toolkit doctor
env-toolkit repair [tool]
env-toolkit config get <key>
env-toolkit config set <key> <value>
env-toolkit remove [tool]
env-toolkit self-uninstall
```

### 12.2 命令说明

- `init`
  - 做系统预检和首次配置
- `install`
  - 根据 profile 或 tool 执行安装计划
- `list`
  - 列出支持的 profile 和工具
- `status`
  - 查看已安装状态、安装目录、版本、PATH 暴露和最近一次执行结果
- `doctor`
  - 输出诊断结果，并对照最近一次执行报告分析漂移项
- `repair`
  - 对损坏或配置不一致的工具执行修复
- `remove`
  - 卸载 toolkit 管理的工具
- `self-uninstall`
  - **卸载 toolkit 自身**。包含：移除 shell 中的 source 注入、删除 `~/.local/share/code-env-toolkit` 等数据与配置目录、删除 bin 目录下的 launcher，实现优雅退场。

### 12.3 交互模式

V1 保留交互式 CLI，但必须同时支持非交互执行：

```bash
env-toolkit install base frontend go --yes
env-toolkit install frontend --node-version 22 --registry npm
env-toolkit install go --goproxy https://goproxy.cn,direct
```

---

## 13. 技术选型

### 13.1 主程序

| 组件 | 选型 | 原因 |
|------|------|------|
| 开发语言 | TypeScript | 类型安全，利于复杂状态建模 |
| CLI 框架 | Commander.js | 命令结构清晰 |
| 交互 | Inquirer 系列能力 | 交互成熟 |
| 配置校验 | Zod 或 JSON Schema | 配置结构明确 |
| HTTP 下载 | 统一 downloader 封装 | 便于代理和重试 |
| 构建 | 打包为平台 launcher | 保证零依赖启动 |
| 测试 | Vitest | 单测与集成测试成本低 |

### 13.2 代码组织

```text
src/
├── cli/                 # 命令定义与入口
├── catalog/             # profile 和 tool 定义
├── planner/             # 任务生成、依赖解析、冲突检测
├── adapters/            # windows/macos/linux 平台实现
├── executor/            # 执行器、checkpoint、恢复
├── validator/           # 预检、后验校验、doctor
├── config/              # 配置与状态读写
├── network/             # 下载、代理、校验
├── shell/               # PATH 与 profile 处理
├── tools/               # 工具级安装逻辑
└── types/               # 类型定义
```

要求：

- `catalog` 只描述工具，不直接做系统调用
- `planner` 不做 IO
- `adapters` 负责系统差异
- `executor` 不关心某个工具的业务规则

---

## 14. 测试策略

### 14.1 单元测试

覆盖：

- tool definition 解析
- 依赖图生成
- 版本策略选择
- PATH 和 shell 片段生成
- 配置合并和默认值

### 14.2 集成测试

覆盖：

- `base` / `frontend` / `go` 安装计划生成
- 不同平台 snapshot 下的策略选择
- 代理配置透传
- checkpoint 恢复

### 14.3 端到端测试

CI 至少覆盖：

- Windows 最新稳定 runner
- macOS 最新稳定 runner
- Ubuntu 最新稳定 runner

验证目标：

- launcher 可安装
- `install base`
- `install frontend`
- `install go`
- `doctor`

对于 GUI 工具安装，CI 只校验安装物存在和命令可发现，不强依赖图形界面启动。

---

## 15. 分阶段实施

### Phase 1：Bootstrap 与基础骨架

- 自包含 launcher 发布链路
- 用户目录与 PATH 管理
- 配置与状态存储
- 下载器、checksum 校验

### Phase 2：Base Profile

- Git
- shell setup
- editor
- terminal
- `doctor` 初版

### Phase 3：Frontend Profile

- FNM
- Node.js
- PNPM
- registry 配置

### Phase 4：Go Profile

- Go 安装
- Go 环境变量
- `gopls` / `dlv`

### Phase 5：Repair 与稳定性

- checkpoint 恢复
- `repair`
- 错误提示细化
- 测试矩阵补全

---

## 16. 风险与应对

### 16.1 GUI 工具跨平台差异大

风险：

- 终端和编辑器在不同系统的安装形式不一致

应对：

- 用“工具类别 + 平台实现”建模
- 不强制三端使用同一产品

### 16.2 用户态安装并非总能覆盖所有工具

风险：

- 某些平台或发行版下，官方只提供系统级安装方式

应对：

- 默认用户态优先
- 明确标记何时需要系统级回退
- 在执行前展示权限需求

### 16.3 下载源不稳定

风险：

- 官方源、镜像源、代理环境都可能不稳定

应对：

- manifest 化下载地址
- checksum 校验
- 失败重试和镜像切换

### 16.4 PATH 与 shell 污染

风险：

- 多次执行后用户环境变量混乱

应对：

- 统一写入 toolkit 管理段
- 幂等更新
- `repair` 和 `doctor` 检查

---

## 17. 结论

V1 最合适的落地方式不是继续扩写一个越来越长的 bootstrap 脚本，而是把项目明确为一个 **跨平台、用户态优先、以编排和验证为核心的开发环境管理工具**。

最终结论如下：

1. **三大系统都进入 V1 正式范围**
2. **基础工具层、前端、Go 是 V1 的三大 profile**
3. **主程序采用自包含 launcher 发布，启动不依赖预装 Node.js**
4. **默认用户态安装，必要时提供系统级安装策略**
5. **通过 Catalog + Planner + Platform Adapter 保证可扩展性**

这份方案可以直接作为后续拆任务和实现的基线设计。
