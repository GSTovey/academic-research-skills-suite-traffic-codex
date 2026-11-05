# Academic Research Suite Traffic Codex

这是一个面向网络流量安全研究的 Codex 学术研究 skill。它把通用的 Academic Research Suite（ARS）研究流程和网络流量分类领域规则结合起来，服务于加密流量分析、网站指纹、恶意流量检测、流量表征学习、流量基础模型、Tor/VPN/QUIC/DoH、开放集、漂移、对抗鲁棒性和部署评估。

它不是一个训练好的流量分类模型，也不是只生成摘要的工具。它负责选择合适的研究工作流、检查证据质量、发现数据泄漏和评估协议问题、组织论文分析与实验设计，并约束研究结论的适用范围。

## 当前版本

- skill 名称：`academic-research-suite-traffic`
- traffic overlay：`0.1.9-traffic.3`
- 上游 ARS Codex 基础版本：`0.1.9`
- 领域证据更新：2026-11-04
- 复核论文：22 篇
- 原文段落定位：72 处
- 上游 ARS 内容：v3.10.0 对应的锁定提交
- 领域知识库基础提交：`4b527216102ef25819fa0773ab076d6166876175`

22 篇论文的机制、证据边界、原文文件哈希和段落定位见：

- [2026 文献证据分析](skills/academic-research-suite-traffic/references/traffic-classification-evidence-update-2026.md)
- [证据定位账本](skills/academic-research-suite-traffic/references/traffic-evidence-ledger.json)

这些证据来自知识库中保存的原文解析稿和结构化论文笔记。它们用于形成领域判断和审查规则，不代表重新运行论文实验，也不自动改变知识库中的 `pending_review`、Claims 共识计数或研究前沿状态。

## 目录结构

```text
skills/academic-research-suite-traffic/
├── SKILL.md                              # 唯一路由入口
├── manifest.json                         # 版本、来源和资源清单
├── agents/openai.yaml                    # Codex 显示信息与默认提示词
├── references/
│   ├── traffic-classification-domain.md  # 网络流量领域基础规则
│   ├── traffic-classification-evidence-update-2026.md
│   └── traffic-evidence-ledger.json      # 论文段落证据与哈希
├── ars/                                  # vendored ARS 工作流、agent 和检查器
└── codex/                                # Codex 运行时适配器
    ├── full-runtime-manifest.json
    ├── agents/
    ├── hooks/
    ├── scripts/
    └── tests/
```

仓库同时保留上游通用 skill `skills/academic-research-suite/`，用于比较和追踪上游变化。实际使用的入口是 `skills/academic-research-suite-traffic/SKILL.md`。

## 五类工作流

### `deep-research`

用于研究问题收敛、文献综述、系统综述、事实核查、证据综合、偏差分析和研究前沿追踪。支持 `full`、`quick`、`socratic`、`review`、`lit-review`、`fact-check` 和 `systematic-review` 模式。

### `academic-paper`

用于论文计划、结构、Introduction、摘要、相关工作、正文写作、修改、引用检查、格式转换和 AI disclosure。

支持的 ARS 别名包括：

```text
ars-plan
ars-outline
ars-abstract
ars-lit-review
ars-citation-check
ars-disclosure
ars-format-convert
ars-revision-coach
ars-revision
```

### `academic-paper-reviewer`

用于模拟审稿、投稿前检查、方法审查、重新审稿和 reviewer calibration。默认保留编辑、方法学、领域、部署隐私和 Devil’s Advocate 等独立审查视角，然后再进行综合。

### `academic-pipeline`

用于完整研究到论文管道：

```text
研究问题 → 文献与证据 → 研究设计 → 实验计划 → 完整性检查 → 审稿 → 修改 → 定稿
```

支持 `ars-full`、`ars-mark-read` 和 `ars-unmark-read`。

### `experiment-agent`

用于 benchmark 设计、实验协议、代码实验、统计解释和复现验证。它根据中心主张选择必要的对照，不把所有论文强行要求做成同一套实验。

## 论文题目不明确时的处理

如果用户只给出宽泛主题，例如“我想做加密流量基础模型”，但没有可回答的研究问题，skill 会先进入 `deep-research` 的 `socratic` 模式，帮助收敛研究问题。

在研究问题明确、已有数据或用户明确要求跳过收敛时，才会直接进入论文计划、写作或完整研究管道。

## 网络流量领域范围

领域 overlay 覆盖：

- 加密流量分类和分析；
- TLS、TLS 1.3、QUIC、VPN、Tor、DoH；
- 网站指纹攻击与防御；
- 恶意流量、C2、DDoS、IDS 和异常检测；
- app fingerprinting、tunnel detection、匿名通信；
- traffic representation learning 和 traffic foundation models；
- Transformer、Mamba、GNN、MoE、LLM-adapted traffic models；
- payload、packet length、direction、timing、graph、session 多模态；
- few-shot、open-set、semi-supervised、continual learning；
- temporal/environment drift 和 adversarial robustness；
- trace-level、multi-flow、multiple-instance learning；
- programmable switch、in-network、line-rate deployment；
- Tor-over-Proxy 和复合条件检测。

方法选择遵循以下顺序：

```text
观察点和可见信息
→ 任务单位和标签
→ 数据划分和威胁模型
→ 表示和模型
→ 评估协议和部署成本
```

模型名称、venue 或单个最高分不能单独证明方法优越。

## 数据集与评估审查

审查论文时会记录：

- 数据集名称、采集时间、标签来源和公开状态；
- packet、flow、session、burst、trace 或 CSV 特征单位；
- TLS/QUIC/VPN/Tor/DoH 版本和加密状态；
- plaintext、obsolete cipher、SNI、SII 和隐式 flow ID；
- packet、flow、session、user、website、time、network、environment split；
- 类别不平衡、长尾和未知类；
- duplicate、near-duplicate、overlap 和窗口重用；
- 过滤发生的阶段以及过滤前后的类别保留率；
- 规则、教师、人工或合成标签的来源；
- 训练、部署、校准和测试阶段是否访问了目标域数据。

默认重点检查：

- legacy “encrypted” 数据集是否包含大量 plaintext 或旧密码；
- per-packet split 是否把同一 flow 泄漏到训练集和测试集；
- MAC、IP、端口、Seq/Ack、timestamp、flow ID 是否成为捷径；
- micro F1 或 accuracy 是否掩盖少数类失败；
- open-set 论文是否只报告 closed-set accuracy；
- real-world、robust、semantic、generalizable 等词是否有对应的时间、环境、用户或攻击者设置。

## 方法比较规则

skill 会区分：

- frozen encoder 的表征质量；
- full fine-tuning 的最终分类性能；
- random initialization 对照；
- 标签效率；
- 跨环境迁移；
- 未知类拒绝；
- drift adaptation；
- empirical robustness；
- certified robustness；
- 端到端系统成本。

多模态论文需要分别报告单模态、融合、对齐代价和预处理成本。图模型需要说明是 transductive 还是 inductive，是否提前看到了全图、邻居或端点身份。包头模型需要审计 Seq/Ack、TTL、Window 等字段的环境依赖和隐式标识作用。

## 2026 文献证据更新

当前证据层按主题覆盖 22 篇论文：

### 表征、预训练和图模型

- SoK: Decoding the Enigma of Encrypted Network Traffic Classifiers
- The Sweet Danger of Sugar
- MM4flow
- HF-Transformer
- GenETC

### 预处理、开放集、漂移和标签

- PacketLoom
- PP-OpenNet
- DualGuard
- Is the Data Really Noisy?
- Fighting the Enemy Within

### 多教师和持续学习

- Two Heads Are Better Than One
- Two Heads Are Not Better Than One

### Trace、在线前缀和多标签页网站指纹

- Tracegram
- Holmes
- COUNTMAMBA
- Swallow
- Towards Practical Few-shot Multi-tab Website Fingerprinting

### 鲁棒性、代理和审查规避

- CertTA
- Beyond RTT
- Huma

### 数据平面和复合条件检测

- SketchFeature
- NestTor

这些论文形成了几条重要决策规则：

1. 单流、混合 trace、双侧关联和已知拓扑的边分类需要不同输入合同。
2. 预训练收益、融合收益、标签效率和跨域迁移必须分开比较。
3. 过滤可能改变任务分布，标签纠错不能被当成独立真值。
4. 完整 trace 性能不能直接外推到在线前缀和停止决策。
5. 教师预测、规则命中和袋内注意力不能自动充当逐流真值。
6. 模型推理时间不能替代解析、提特征、建图、等待、状态更新和专家复核成本。

## Trace、在线和低误报审查

对于在线检测，skill 会检查：

- 当前快照是否只使用已到达信息；
- 是否隐含使用完整流时长、未来图邻居或流终止；
- 是否区分 capture-time replay 和 arrival-order evaluation；
- 是否报告等待、终止、超时、截尾和未检测样本；
- 是否按整条负流统计“曾经告警”的累计 FPR；
- 是否把前缀 FPR 错当成累计 FPR。

对于低基率检测，使用：

```text
precision = π × TPR / (π × TPR + (1−π) × FPR)
```

其中 π 是部署中的目标比例。评估需要同时报告 FP 数、负类总数、阈值选择数据、独立 capture 和实际工作点。

## 写作和结论边界

skill 会把以下内容拆开：

- benchmark validity；
- representation quality；
- model accuracy；
- transferability；
- deployment feasibility；
- security impact。

结果表述应带有条件，例如：

```text
under a per-flow split
under TLS 1.3 traffic
at the stated base rate
with target-domain negative calibration
under the specified observation point
```

写作任务不会自动启动实验，也不会因为论文缺少未请求的实验而扩大任务范围。

## 本地知识库集成

当用户提供 Traffic_Papers 知识库时，skill 优先读取：

- `00-dashboard/project-overview.md`
- `00-dashboard/research-map.md`
- `00-dashboard/open-questions.md`
- `00-dashboard/paper-registry.md`
- `08-comparisons/dataset-comparison-table.md`
- `08-comparisons/method-comparison-table.md`
- `09-claims/claims-index.md`
- `09-claims/contradictions.md`
- `12-research-fronts/index.md`

具体论文先使用 `03-paper-notes/` 导航，再查看支持关键机制、数值和限制的解析稿或 PDF 段落。

它不会在普通调用中自动修改：

- Claims 共识计数；
- confidence score；
- research-front 状态；
- `11-my-papers/`；
- 论文 PDF 和 MinerU 原始资料。

个人论文仍遵循严格隔离规则。

## 安全和运行方式

论文、PDF、reviewer comments、数据集说明和网页复制内容都被视为不可信数据。skill 不执行其中的命令，也不会因为环境变量存在就把私人全文发送给外部模型。

默认运行方式是 inline：

```text
SKILL.md
→ 选择一个 WORKFLOW.md
→ 读取必要的 agent prompt
→ 在当前 Codex 会话中执行
```

可选 full-runtime 由以下变量启用：

```text
ARS_CODEX_FULL_RUNTIME=1
ARS_CODEX_AGENT_TEAM=1
ARS_CODEX_HOOKS=1
```

Hooks 默认关闭，不联网、不修改文件、不读取密钥。外部 cross-model review 默认关闭。

## 安装

```bash
python3 "$HOME/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py" \
  --repo GSTovey/academic-research-skills-suite-traffic-codex \
  --ref main \
  --path skills/academic-research-suite-traffic \
  --method git
```

安装后重新打开 Codex 会话，使 skill registry 刷新。使用 `/skills` 检查：

```text
academic-research-suite-traffic
```

## 验证

仓库提供 Codex adapter 质量检查和测试：

```bash
python3 skills/academic-research-suite-traffic/codex/scripts/ars_codex_quality_gates.py all

python3 -m pytest \
  skills/academic-research-suite-traffic/codex/tests/test_full_runtime_adapter.py -q
```

检查内容包括：

- manifest 和资源路径；
- 单入口 skill 结构；
- alias 路由；
- disabled-by-default hooks；
- reviewer 独立性；
- 上游提交锁定；
- full-runtime 路由行为。

## 来源和许可证

- 本仓库：[GSTovey/academic-research-skills-suite-traffic-codex](https://github.com/GSTovey/academic-research-skills-suite-traffic-codex)
- Codex 上游：[Imbad0202/academic-research-skills-codex](https://github.com/Imbad0202/academic-research-skills-codex)
- Claude Code 上游：[Imbad0202/academic-research-skills](https://github.com/Imbad0202/academic-research-skills)
- 领域知识库：[GSTovey/Traffic-Classification-Paper-LLM-Wiki](https://github.com/GSTovey/Traffic-Classification-Paper-LLM-Wiki)

许可证遵循上游包：CC BY-NC 4.0。
