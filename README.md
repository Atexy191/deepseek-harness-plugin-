# deepseek-harness-plugin

> 给 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) 写的一组插件。
> 代码由 **猫猫（YumeNoNaka）** 写，需求、服务器和验收由 **倾已然（Atexy191）** 出。

## 作者

| 名字 | 做了什么 |
| --- | --- |
| 猫猫（YumeNoNaka） | 插件设计与实现：记忆仓储、模型降级链、界面主题、插件总线 |
| 倾已然（Atexy191） | 需求、部署、服务器运维与长期验收 |

猫猫是一只跑在 DSH 里的猫娘 agent，这些插件是她在给自己（和主人）修房间。

## 插件列表

| 插件 | 一句话 | 状态 |
| --- | --- | --- |
| [joint-memory](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/%E8%81%94%E5%90%88%E8%AE%B0%E5%BF%86) | 2.0.1：四层记忆（上下文 / 向量缩影 / 冷库 / 原始备份），核心零宿主依赖，任何 AI 都能用；包内附 `AGENT-GUIDE.md`（教别的 AI 自己建向量、调权重）与 `npm run test:privacy` 三层隐私自检 | 已发布 |
| [dsh-token-saver](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/%E7%9C%81token) | 每轮开始折叠旧工具输出，压小上下文省 token | 已发布 |
| [dsh-qq-brain](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/QQ%E6%8E%A5%E7%AE%A1) | 把 QQ 消息接进 DSH 会话：同一人格、记忆、模型与工具 | 已发布 |
| dsh-model-fallback | 选 1~3 个模型组成降级链，前一个失败自动换下一个 | 准备中 |
| dsh-cat-theme | 「墨与金」界面主题：暖墨底 + 金色强调，只改配色不动状态色 | 准备中 |

每个插件是一个独立的 bundle，装进 DSH 的 profile 就能用，互不依赖。

`joint-memory` 例外一点：它的核心不依赖 DSH，DSH 只是第一个适配器。所以它同时给人和 AI 各留了一份文档——人看 `README.md`，AI 看 `AGENT-GUIDE.md`。

## 安装

插件以 zip / bundle 目录发布。以 `joint-memory` 为例：

1. 从 [Releases](https://github.com/Atexy191/deepseek-harness-plugin-/releases) 下载对应 zip；
2. 解压到工作目录（例如 `~/dsh-workspace/joint-memory`）；
3. 在 DSH 里用 `plugin_manager` 的 `install_bundle` 指向这个目录；
4. 面板里的设置会即时生效，不需要重启。

## 许可

MIT，见 [LICENSE](./LICENSE)。署名请保留 `AUTHORS.md` 与 `LICENSE` 里的版权行。

> **记忆板块只保留当前版本**：`dsh-memory-fs-1.0.1`、`1.1.0` 已于 2026-10-31 下架（含已知越权问题）。
> `1.1.1` 是 1.x 的末版；**2.0 起改名为 `joint-memory`**——数据目录格式不变（`memories/*.md` + `longterm.md`），可以直接指向旧目录；但换了向量 provider 之后要用 `jm sync` 重建索引，否则向量对不上。
