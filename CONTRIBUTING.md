# 贡献指南

感谢你想为 AperturePrism-AI-Review 做贡献！这是一个独立开发的 GitHub Issue 分析与 Pull Request 审查平台。以下约定能帮你更快地提交一个可被合并的 PR。

## 环境准备

- Node.js ≥ 22（`package.json` 已声明 `engines.node`）
- PostgreSQL（需启用 `pgvector` 扩展）+ Redis
- 建议使用 nvm / fnm 管理 Node 版本

```bash
npm install
npm run build
cp .env.example .env   # 按需填写 DATABASE_URL / REDIS_URL / GITHUB_* / 模型配置
# ⚠️ .env 含明文密钥，已被 .gitignore 忽略，切勿提交到仓库。
node scripts/migrate.mjs
```

> 首次安装用 `npm install`；验证 / CI 用 `npm ci`（干净、可复现，见下）。

本地起服务（各占一个终端）：

```bash
npm run dev --workspace apps/api
npm run dev --workspace apps/analysis-worker
npm run dev --workspace apps/index-worker
npm run dev --workspace apps/scheduler
cd apps/web && npm install && npm run dev   # Web（独立 workspace，端口 5173）
```

## 提交前自检

```bash
npm run typecheck     # 全 workspace 类型检查
npm run test          # 先 build 再跑 vitest（DB 集成测试在未配置 APERTUREPRISM_INTEGRATION_DATABASE_URL 时自动跳过）
npm run lint          # ESLint
npm run format:check  # Prettier 校验；直接格式化用 npm run format
```

`apps/web` 是独立 workspace（有独立 `package-lock.json`），单独验证：

```bash
cd apps/web && npm ci && npm run typecheck && npm test
```

## 提交与分支规范

- 分支命名：`fix/…`、`feat/…`、`docs/…`、`chore/…`、`ci/…` 均可，短横线分隔，如 `fix/sse-task-event`。
- Commit message 遵循仓库既有约定（Conventional Commits + 中文说明）：

  ```
  fix(event-stream): 补齐 publishing 状态映射
  feat(web): 新增 XX 功能
  docs(readme): 修正 XX
  chore: 升级依赖
  ```

- 一个 PR 只做一件事：功能、修复、文档分开提交，便于 review 与 cherry-pick。

## PR 检查清单

- [ ] 描述清楚「问题是什么、改了什么、怎么验证」
- [ ] 涉及行为变更时补充/更新单测
- [ ] `npm run typecheck` 通过
- [ ] `npm run test` 通过
- [ ] `npm run lint` 通过
- [ ] `npm run format:check` 通过
- [ ] `apps/web`（若改动前端）`npm run typecheck && npm test` 通过
- [ ] 相关文档（README / docs）同步更新

## 参考

- 架构与模块边界见 `docs/APERTUREPRISM_AI_REVIEW_PROJECT_DESIGN.md`
- 运维见 `docs/RUNBOOK.md`
- 环境变量全集见 `.env.example`
