<p align="center">
  <h1 align="center">Uptimed</h1>
  <p align="center">
    开源、自托管的监控平台 —— 集运行时间监控、智能告警、事件管理与状态页于一体。
    <br />
    <em>"Get Uptimed."</em>
  </p>
  <p align="center">
    <a href="https://github.com/uptimed/uptimed/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License" /></a>
    <a href="https://github.com/uptimed/uptimed/releases"><img src="https://img.shields.io/github/v/release/uptimed/uptimed" alt="Release" /></a>
    <a href="https://github.com/uptimed/uptimed/actions"><img src="https://img.shields.io/github/actions/workflow/status/uptimed/uptimed/ci.yml?branch=main" alt="CI" /></a>
    <a href="https://uptimed.dev"><img src="https://img.shields.io/badge/website-uptimed.dev-green" alt="Website" /></a>
  </p>
  <p align="center">
    <a href="./README.md">English</a> · <a href="./README.zh-CN.md">中文</a>
  </p>
</p>

---

## 为什么选择 Uptimed？

[Uptime Kuma](https://github.com/louislam/uptime-kuma)（84K+ Stars）是最流行的自托管监控工具，但其核心架构问题 **4 年未解决**：

| 痛点 | Uptime Kuma | Uptimed |
|------|-------------|---------|
| 多用户 & RBAC | 仅单用户 | 多用户 + 团队 + 角色权限 |
| REST API | 无（仅 WebSocket） | REST + OpenAPI 3.1，一等公民 |
| 性能 | 1000 监控器卡顿 | Go 协程池，5000+ 监控器轻松应对 |
| 事件管理 | 无 | 完整生命周期 + 事后复盘 |
| 配置即代码 | 不支持 | YAML 配置，Git 友好 |
| 多区域探测 | 不支持 | 远程 Agent，多地域探测 |
| 数据库 | SQLite / MariaDB | SQLite → PostgreSQL → TimescaleDB 渐进扩展 |

## 功能特性

- **多协议监控** — HTTP(s)、TCP、Ping、DNS、WebSocket、Push、Docker、SSL 证书
- **智能告警** — 告警分组、降噪、升级策略、依赖链分析
- **事件管理** — 完整生命周期、时间线、事后复盘
- **公共状态页** — 自定义品牌、独立域名、嵌入式组件
- **多用户 & 团队** — RBAC 角色权限控制
- **REST API** — OpenAPI 3.1 规范，完整 CRUD
- **实时推送** — SSE（Server-Sent Events），自动重连
- **配置即代码** — YAML 定义监控器，Git 管理
- **多区域探测** — Go Agent 单文件 < 10MB，部署到任意区域
- **可插拔通知** — Telegram、Slack、Email、Webhook、飞书、钉钉 等
- **渐进扩展数据库** — SQLite（默认） → PostgreSQL → TimescaleDB → VictoriaMetrics

## 技术栈

| 层 | 选型 |
|----|------|
| 后端 | Go（单二进制，< 50MB Docker 镜像） |
| 前端 | React 19 + TypeScript + shadcn/ui + Tailwind CSS 4 |
| 路由 | chi + huma/v2（自动生成 OpenAPI） |
| 默认数据库 | SQLite（零配置，一行命令启动） |
| 扩展数据库 | PostgreSQL + TimescaleDB |
| 实时通信 | SSE（Server-Sent Events） |
| 容器化 | Docker / Docker Compose |

## 快速开始

### Docker（60 秒部署）

```bash
docker run -d \
  --name uptimed \
  --restart always \
  -p 3000:3000 \
  -v uptimed-data:/data \
  ghcr.io/uptimed/uptimed:latest
```

启动后访问 `http://your-server:3000`，按向导创建管理员账号。

### Docker Compose（推荐）

```yaml
# docker-compose.yml
services:
  uptimed:
    image: ghcr.io/uptimed/uptimed:latest
    container_name: uptimed
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - uptimed-data:/data
    environment:
      SECRET_KEY: "change-me-use-openssl-rand-hex-32"
      DISABLE_SIGNUP: "false"   # 初始化完成后改为 true
      LOG_LEVEL: "info"

volumes:
  uptimed-data:
```

```bash
docker compose up -d
```

### 从源码构建

```bash
git clone https://github.com/uptimed/uptimed.git
cd uptimed
make build
./uptimed server
```

## 配置即代码

```yaml
# uptimed.yml
monitors:
  - name: "生产环境 API"
    type: http
    url: https://api.example.com/health
    interval: 30s
    timeout: 10s
    retries: 3
    assertions:
      - status == 200
      - responseTime < 500ms
      - jsonpath $.status == "ok"
    tags: [production, api, critical]
    alerts:
      - channel: telegram
        after: 3 failures
      - channel: email
        after: 5 failures
        escalate: true
```

## 数据库扩展

```bash
# 默认：SQLite（个人 / HomeLab，< 500 监控器）
DATABASE_URL=sqlite:///data/uptimed.db

# 扩展：PostgreSQL（500 - 5,000 监控器）
DATABASE_URL=postgres://user:pass@host/uptimed

# 高级：TimescaleDB（5,000 - 50,000 监控器，自动降采样）
DATABASE_URL=postgres://user:pass@host/uptimed?timescale=true
```

## 路线图

| 阶段 | 版本 | 内容 |
|------|------|------|
| Phase 0 — 基础 | v0.1.0 | 项目脚手架、CI/CD、存储抽象层 |
| Phase 1 — 可用 | v0.5.0 | 替代 Uptime Kuma 核心功能 |
| Phase 2 — 差异化 | v1.0.0 | 智能告警、事件管理、多区域探测 |
| Phase 3 — 生态 | v1.x+ | 社区生态、插件系统 |

## 贡献

欢迎贡献！请查看 [CONTRIBUTING.md](docs/contributing.md) 了解详情。

```bash
# 开发环境启动
make dev

# 运行测试
make test

# 代码检查
make lint
```

## 许可证

[MIT License](LICENSE) — 由 **SKY FLUX** 维护。
