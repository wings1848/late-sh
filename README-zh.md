# late.sh

一个舒适的终端俱乐部，面向计算机爱好者。聊天、音乐、游戏、艺术、编程和技术新闻。用任何 SSH 客户端即可连接！

```bash
ssh late.sh
```

`late.sh` 是一个终端优先的社交应用：实时聊天、音乐、游戏、新闻、个人资料，以及一个共享的永远在线空间，你可以从任何 SSH 客户端进入。

## 项目状态

本仓库是 `late.sh` 的主要代码库。

- 项目开放供源代码阅读、本地开发、审计和贡献。
- 公共托管服务 `late.sh` 仍然是官方部署。
- 代码在 FSL 保护期内为源码可见（source-available），非 OSI 开源。

详情请阅读 [LICENSE](LICENSE)、[LICENSING.md](LICENSING.md) 中的简明政策，以及 [CONTRIBUTING.md](CONTRIBUTING.md) 中的贡献规则。

## 包含内容

- SSH TUI，包含仪表盘、聊天、个人资料、新闻和街机屏幕
- 实时全球聊天和共享活动流
- 通过 Icecast/Liquidsoap 进行音频流传输，支持浏览器和 CLI 配对
- 终端游戏，包括 2048、数独、非ogram、扫雷和纸牌
- Web 前端，用于落地页、连接流程和配对客户端体验
- 配套 CLI，用于本地音频播放和同步可视化数据

## 工作区

这是一个包含四个 crate 的 Rust 工作区：

| Crate | 角色 |
|-------|------|
| `late-cli` | 配套 CLI，用于本地音频播放、配对控制和可视化同步 |
| `late-core` | 共享领域代码、数据库层、迁移和基础设施辅助 |
| `late-ssh` | SSH 服务器和终端 UI 应用 |
| `late-web` | Web 服务器、落地页、连接流程和浏览器配对 |

后端由 PostgreSQL、Icecast 和 Liquidsoap 支持。

## 快速开始

试用在线服务：

```bash
ssh late.sh
```

SSH 登录名会被丢弃，不会用作公开显示名称。首次连接时，新账户会获得一个随机的修饰词+名词用户名；之后可以在设置中更改用户名。

自行运行（需要 Docker）：

```bash
git clone https://github.com/mpiorowski/late-sh
cd late-sh
make start
```

然后连接到本地实例：

```bash
ssh localhost -p 2222
```

就这样。Postgres、Icecast 和 Liquidsoap 都会自动启动。

## 配套 CLI

安装配套 CLI 以进行本地音频播放和同步可视化：

macOS / Linux / Termux：

```bash
curl -fsSL https://cli.late.sh/install.sh | bash
```

在 Termux 上，安装程序会获取 Android CLI 构建版本，而非 GNU/Linux 版本。

Windows PowerShell (x64)：

```powershell
irm https://cli.late.sh/install.ps1 | iex
```

Nix / NixOS：

```bash
nix run github:mpiorowski/late-sh#late
```

或从源码构建：

```bash
mise install        # 可选 — 设置预期的 Rust 工具链
cargo build --release --bin late
```

## 本地开发

如果不想用 Docker 包装 Rust 构建，可以在 Docker 中运行基础设施，在本地原生运行应用：

```bash
docker compose up -d postgres icecast liquidsoap
cargo run -p late-ssh
cargo run -p late-web
```

本地主机开发可以使用 Cargo 的正常默认设置，包括标准的仓库本地 `target/` 目录。`/app/target` 路径仅用于 Docker/开发容器。

```bash
export CARGO_HOME=$HOME/.cargo
```

使用 `mise install` 获取预期的 Rust 工具链、`mold` 链接器和 `cargo-nextest`。

## 验证

在迭代过程中运行快速本地检查：

```bash
make check
```

这将运行 `cargo fmt --check`、`cargo clippy` 和 `cargo nextest`。
本地检查会在端口 `55433` 上启动一个专用的 Compose Postgres 项目（`late-check`），并通过 `TEST_DATABASE_URL` 将需要数据库的测试指向它。
如果需要并行检查数据库，可以覆盖 `CHECK_INSTANCE` 或 `CHECK_PG_HOST_PORT`。

在提交 PR 之前运行更广泛的 PR 风格检查：

```bash
make checkci
```

## 贡献

欢迎贡献，但请先阅读项目政策：

- [CONTRIBUTING.md](CONTRIBUTING.md)
- [LICENSING.md](LICENSING.md)
- [LICENSE](LICENSE)

本仓库使用 DCO 签核提交：

```bash
git commit -s
```

如果你分发分支版本，请不要将其宣称为官方 `late.sh` 服务，也不要用项目品牌作为自己的品牌。

## 更多背景

- [CONTEXT.md](CONTEXT.md) — 架构、不变量和工作上下文。专为 LLM 编写 — 将此文件提供给 AI 编辑器以获得最佳效果。
- [CONTRIBUTING.md](CONTRIBUTING.md) — 工作流程、测试规则、模块模式和 AI 辅助开发技巧。
- [THEME.md](THEME.md) — 如何通过 PR 贡献新的内置 SSH 主题。
- [late-cli/README.md](late-cli/README.md) — CLI 特定用法和行为。
- [README-zh.md](README-zh.md) — 本文件，简体中文版 README。
