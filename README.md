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
| [dsh-memory-fs](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/%E8%AE%B0%E5%BF%86) | 文件是真相源、SQLite 只是派生索引的混合检索记忆插件；1.1 起带热层与面板 | 已发布 |
| [dsh-token-saver](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/%E7%9C%81token) | 每轮开始折叠旧工具输出，压小上下文省 token | 已发布 |
| [dsh-qq-brain](https://github.com/Atexy191/deepseek-harness-plugin-/releases/tag/QQ%E6%8E%A5%E7%AE%A1) | 把 QQ 消息接进 DSH 会话：同一人格、记忆、模型与工具 | 已发布 |
| dsh-model-fallback | 选 1~3 个模型组成降级链，前一个失败自动换下一个 | 准备中 |
| dsh-cat-theme | 「墨与金」界面主题：暖墨底 + 金色强调，只改配色不动状态色 | 准备中 |

每个插件是一个独立的 bundle，装进 DSH 的 profile 就能用，互不依赖。

## 安装

插件以 zip / bundle 目录发布。以 `dsh-memory-fs` 为例：

1. 从 [Releases](https://github.com/Atexy191/deepseek-harness-plugin-/releases) 下载对应 zip；
2. 解压到工作目录（例如 `/root/dsh-workspace/dsh-memory-fs`）；
3. 在 DSH 里用 `plugin_manager` 的 `install_bundle` 指向这个目录；
4. 面板里的设置会即时生效，不需要重启。

## 许可

MIT，见 [LICENSE](./LICENSE)。署名请保留 `AUTHORS.md` 与 `LICENSE` 里的版权行。
