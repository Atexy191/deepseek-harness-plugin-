# deepseek-harness-plugin

> 给 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) 写的一组插件。
> 代码由 **猫猫（YumeNoNaka）** 写，需求、服务器和验收由 **倾已然（Atexy191）** 出。

## English (short)

Plugins for [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness). Written by **maomao (YumeNoNaka)**, specified and deployed by **Atexy191**.

**`joint-memory v2` (0.1.4) — long-term memory that any AI agent can use.** Four layers, one source of truth:

```
context (host window) -> index/experiences -> cold store (pre-compaction summaries) -> disk backup (full sessions)
```

Read order is fixed; when nothing is found it says so instead of inventing. Decay applies to the index layer only — **backup never decays**. Zero runtime dependencies, Node >= 20, MIT.

## 记忆插件：joint-memory v2（当前版本）

**0.1.4 是唯一在维护的版本**（0.1.4 只加可观测性：召回健康账 `recall-health.json` + `jmv2_stats.recallHealth` + 面板红字，召回行为与 0.1.3 一致）。** v1（`joint-memory 0.0.x`，CLI 形态）与 v2 `0.1.2` 已于 2026-11-06 下架，不再提供下载。

- **四层读阶梯**：上下文 → 索引/经验 → 冷库（压缩前总结，不存全文）→ 硬盘备份（完整会话）→ 明确说「你并没有这段记忆」。
- **衰减只在索引层**（艾宾浩斯 `R = 0.5^(t/H)`，`H = 7 天 × (1 + 召回次数)`）；**备份层永久，不参与衰减**。
- **12 个工具**（`write` / `recall` / `fetch` / `list` / `update` / `forget` / `signal` / `sync` / `stats` / `snapshot` / `rollback` / `hot`）；`snapshot` / `rollback` 先逐文件校验 sha256 再动手；`forget` 默认只预览。
- **DSH 适配器补工具做不到的部分**：热层注入（`hot.md`，只注入 `pub` 段，`priv` 段永不进提示）、每轮自动召回、右下角面板、群聊会话读不到 `private`。
- **markdown 是唯一真相**：索引、缓存、向量全部是派生的，删了用 `sync` 重建。
- **零依赖**：一个 zip 解开就能跑，没有 `npm install`。`npm test` 189 项全绿。

**下载**：[joint-memory v2 0.1.4](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/v2-0.1.4) — 121,546 B / 28 文件，sha256 `43fed1925ac1d3c1c46110cd6e925d8c5e51e37085dab187b4f4d4dac48b2184`

发布页有完整说明：它解决什么问题、这一版修了什么（含两个静默 bug 的根因）、怎么装、装完怎么四条自查、包内结构、已知限制、从 v1 怎么迁、数据在哪、卸载会不会删数据。包内另有 `GUIDE.md`（装法+排错）、`DESIGN.md`（为什么这么设计）、`CHANGELOG.md`（逐版）。

## 召回健不健康，一眼看得见

自动召回是锦上添花的东西，失败时按设计不报错——但**不报错不等于看不见**。

0.1.4 起，适配器每轮把一次求值写进 `<root>/recall-health.json`：`outcome`
（`injected` / `empty` / `skipped` / `error` / `timeout`）、命中条数、查询长度、耗时、
是否走了关键词兜底、错误摘要，外加最近 20 条。**不存查询正文，只存长度。**

`jmv2_stats` 会把它当 `recallHealth` 一起回（含 `consecutiveEmpty` / `consecutiveError` /
`stale`），DSH 右下角面板也会亮一行：连续空 ≥ 5 或异常 ≥ 1 就变红。

```
outcome=injected   真塞进去了
outcome=empty      查了但没命中（no-hits）
outcome=skipped    没查或有命中没塞（query-too-short / deduped / budget）
outcome=error      真出事了，看 last.error
```

「跳过」不算失败：一句「嗯」不该把警报清零，也不该让假警报淹掉真警报。

## 插件列表

| 插件 | 一句话 | 状态 |
| --- | --- | --- |
| [joint-memory v2](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/v2-0.1.4) | 0.1.4：四层长时记忆 + 12 工具 + MCP stdio + DSH 适配器（热层注入 / 每轮自动召回 / 面板）；`priv` 段永不进提示；备份永不衰减；召回健康账（失败不再长得像「没记忆」）。**121,546 字节 / 28 文件**，189 项测试全绿 | 已发布（推荐） |
| [dsh-token-saver](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/%E7%9C%81token) | 每轮开始折叠旧工具输出，压小上下文省 token | 已发布 |
| [dsh-qq-brain](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/QQ%E6%8E%A5%E7%AE%A1) | 把 QQ 消息接进 DSH 会话：同一人格、记忆、模型与工具 | 已发布 |
| dsh-model-fallback | 选 1~3 个模型组成降级链，前一个失败自动换下一个 | 准备中 |
| dsh-cat-theme | 「墨与金」界面主题：暖墨底 + 金色强调，只改配色不动状态色 | 准备中 |

每个插件是一个独立的 bundle，装进 DSH 的 profile 就能用，互不依赖。

`joint-memory` 例外一点：它的核心不依赖 DSH，DSH 只是第一个适配器。所以它同时给人和 AI 各留了一份文档——人看 `README.md`，AI 看 `GUIDE.md`。

## 安装

插件以 zip / bundle 目录发布。以 `joint-memory v2` 为例：

1. 从 [Releases](https://github.com/Atexy191/deepseek-harness-plugin-/releases) 下载对应 zip；
2. 解压到工作目录（例如 `~/dsh-workspace/joint-memory-v2`），包解开就是根目录：`src/` + `adapters/` + `GUIDE.md`；
3. 在 DSH 里用插件总线（`install_bundle`）指向这个目录，或在 `cordis.patch.yml` 里挂 `joint-memory-v2/adapters/dsh`（片段见 `GUIDE.md`）；
4. 面板里的设置即时生效，不需要重启。

其他支持 MCP stdio 的宿主直接跑 `JOINT_MEMORY_DIR=$HOME/.joint-memory node src/mcp/serve.mjs` 即可。

## 许可

MIT，见 [LICENSE](./LICENSE)。署名请保留 `AUTHORS.md` 与 `LICENSE` 里的版权行。

## 记忆板块的版本线

> **只保留当前版本。** 2026-11-06 起下架了 `joint-memory 0.0.10`（v1，CLI + `jm serve` 形态）、`超轻量化记忆插件 0.0.4`（v1 的最小内核）和 v2 `0.1.2`（自动召回静默失效）。
> 更早的 `dsh-memory-fs`：`1.0.1`、`1.1.0` 已于 2026-10-31 下架（含已知越权问题），`1.1.1` 是 1.x 末版。2.0 起改名为 `joint-memory`，数据目录格式不变（`memories/*.md` + `longterm.md`）；换向量 provider 后需 `jm sync` 重建索引。
