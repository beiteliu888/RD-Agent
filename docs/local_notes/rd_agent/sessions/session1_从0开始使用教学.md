# Session 1: 从 0 开始使用 RD-Agent

## 目标

帮助第一次接触 RD-Agent 的用户知道：它是什么、要准备什么、哪些会收费、哪些不能先碰、怎么安全地跑第一个最小检查。

## 你先要知道的 5 件事

1. 【事实】RD-Agent 当前官方写明只支持 Linux。
2. 【事实】大多数真实 workflow 需要 chat model 和 embedding model，也就是通常需要 API key 和费用。
3. 【事实】多数 scenario 使用 Docker 执行代码。
4. 【事实】Quant workflow 依赖 Qlib 和本地金融数据；Kaggle workflow 依赖 Kaggle 账号/token/competition join。
5. 【建议】第一次不要直接跑 `rdagent fin_quant`，先做无密钥本地检查。

## 先回答 4 个容易卡住的问题

| 问题 | 答案 |
| --- | --- |
| RD-Agent 是 Codex 吗？ | 不是。Codex 可以作为外部助手帮你启动或研究它，但 RD-Agent 运行后由自己的 loop 控制。 |
| RD-Agent 是 MCP 吗？ | 不是。它主要是 Python 项目内部的 workflow、模块、prompt、runner 和执行环境。 |
| RD-Agent 接什么模型？ | 它通过 LiteLLM 接 chat model 和 embedding model，不绑定固定模型。 |
| `runtime` 是什么意思？ | 简单说，就是程序跑起来后负责调度流程的“运行底座”：谁先做、谁后做、状态存哪里、失败后怎么记录、下一轮怎么继续。 |

一句话记忆：**RD-Agent = LLM + workflow runtime + 场景规则 + 可执行实验环境。**

## 第 0 步：选学习目标

| 目标 | 推荐 scenario | 原因 |
| --- | --- | --- |
| 学它怎么做 R&D agent loop | 先读 `fin_factor` 或 `data_science` | 流程比较典型。 |
| 学 quant factor/model 自动研发 | `fin_factor` 后再 `fin_quant` | `fin_quant` 更完整也更重。 |
| 学 Kaggle/MLE agent | `data_science` | 文档对 dataset 格式讲得最细。 |
| 学 UI / trace audit | `rdagent ui` 或 `server_ui` | 需要先有 log。 |

## 第 1 步：准备环境

| 项 | 推荐 |
| --- | --- |
| OS | Linux。macOS 先不要假设完整可跑。 |
| Python | 3.10 或 3.11。 |
| 安装方式 | 普通用户用 `pip install rdagent`；开发者用源码和 `make dev`。 |
| Docker | 真实 workflow 前确认 `docker run hello-world` 可无 sudo 运行。 |
| `.env` | 先不要创建真实 `.env`，等明确要调用哪个 provider。 |

## 第 2 步：理解会不会花钱

| 项 | 免费吗 | 说明 |
| --- | --- | --- |
| 阅读代码和 docs | 是 | 本地静态阅读无成本。 |
| CLI help / port-only check | 通常是 | 不应调用外部 API。 |
| LLM chat | 通常不是 | OpenAI/Azure/DeepSeek 等可能收费。 |
| embedding | 通常不是 | 也可能是外部 provider。 |
| Docker | 本身不一定收费 | 但可能拉镜像、占磁盘/算力。 |
| Kaggle | 账号免费但有规则 | token、competition join 和数据许可要谨慎。 |
| Qlib 数据 | 数据源和下载另算 | 需要确认来源、许可、point-in-time。 |

## 第 3 步：第一次安全检查

【建议】第一次只做这类检查：

```text
目标：确认 RD-Agent 是否可启动基本 CLI，不调用 LLM、不启动 Docker、不下载数据。
边界：不写真实 .env，不使用 API key，不运行 scenario agent loop。
```

可选检查：

| 检查 | 目的 | 风险 |
| --- | --- | --- |
| 查看 CLI help | 确认命令入口存在。 | 低。 |
| `health_check --no-check-env --no-check-docker` | 只检查端口等轻量项。 | 低。 |
| 查看 package metadata | 确认 Python 版本和 entrypoint。 | 低。 |

## 第 4 步：选择第一个真实 scenario

### 方案 A：Quant factor 学习

适合：想理解 factor research loop。

运行前需要：

- LLM chat model key。
- embedding model key。
- Linux。
- Docker 或 Conda 执行环境。
- Qlib 数据。
- 实验账本。

不建议第一次直接跑完整 `fin_quant`，因为 factor + model joint loop 更复杂。

### 方案 B：Data Science custom dataset

适合：想理解 agent 怎么针对一个 dataset 做 feature/model 迭代。

运行前需要：

- local dataset 结构。
- `description.md`。
- 可选 `sample.py`、`valid.py`、`grade.py`。
- LLM/embedding config。
- Docker 或 Conda 执行环境。

### 方案 C：Kaggle

适合：想复现 MLE-bench/Kaggle agent。

运行前需要：

- Kaggle 账号。
- `kaggle.json` token。
- 加入 competition。
- 接受 competition rules。
- 数据下载路径。

【建议】这不是第一轮最安全路径。

## 第 5 步：运行前实验账本模板

每次运行前填这个：

| 字段 | 内容 |
| --- | --- |
| Batch / session name |  |
| Goal |  |
| Hypothesis |  |
| Author-intended workflow or local protocol |  |
| Controlled variables |  |
| Changed variables |  |
| Expected outcome |  |
| Commands / entrypoints |  |
| Data dependency |  |
| External API / cloud dependency |  |
| Secrets required? | yes/no |
| Expected runtime |  |
| Failure modes |  |
| Stop condition |  |

## 第 6 步：看结果

| 你要找什么 | 位置 |
| --- | --- |
| 运行日志 | `log/<timestamp>/` |
| 可恢复 session | `log/<timestamp>/__session__/` |
| 生成代码/任务 workspace | `git_ignore_folder/RD-Agent_workspace/<uuid>` |
| UI 查看 | `rdagent ui --port <port> --log-dir <log_dir>` |
| Web UI | `web/` build 后配合 `rdagent server_ui` |

## 第 7 步：第一次学习结论怎么写

每次结束后写：

| 问题 | 回答 |
| --- | --- |
| What we confirmed |  |
| What is still unclear |  |
| What came from official docs |  |
| What came from code |  |
| What came from our inference |  |
| What should be tested next |  |
| What this implies for our own AI alpha research system |  |

## 本 session 的结论

- 【事实】RD-Agent 的使用门槛主要来自 Linux、Docker、LLM/embedding key、数据准备和日志理解。
- 【建议】第一次体验应从 no-key preflight 开始。
- 【建议】面向 AI alpha research system 的学习重点不是“如何马上跑出 alpha”，而是学习它如何组织 hypothesis、experiment、code、feedback 和 trace。
