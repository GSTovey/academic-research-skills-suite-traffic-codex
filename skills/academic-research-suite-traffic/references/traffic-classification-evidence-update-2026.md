# 2026 流量安全文献：机制、条件与 Skill 决策

更新：2026-11-04。按任务查阅下表对应的证据编号；不需要每次加载全文。

本次针对知识库扩展后的关键问题，复核了 **22 篇论文的方法、评估或限制段落**，其中包含 2026-10 新入库论文的原文。这里的原文指本地保存的 MinerU 解析稿；没有重跑论文实验，也没有对全库逐篇重新精读。来源身份、文件哈希、段落行号及段落哈希见 [traffic-evidence-ledger.json](traffic-evidence-ledger.json)。`source_passage_checked` 仅表示这些机制和边界与已读段落一致，不等于独立复现或出版元数据已核验；知识库原有 `pending_review`、共识计数和个人论文隔离状态保持不变。

基础语料 Git 提交为 `4b527216102ef25819fa0773ab076d6166876175`，实际使用了其后的本地未提交资料，因此不能用该提交单独重建此次输入。账本的逐文件哈希用于辨认版本；不随 skill 分发 PDF 或全文。下面的方法描述属于论文报告，边界包含作者限制及本次条件分析，执行规则是从这些证据提炼的建议。

## 按任务加载

| 当前问题 | 查阅证据 | 先回答的判断 |
|---|---|---|
| 表征、预训练、包头或多模态是否有用 | E01–E05 | 比较究竟改变了表示、监督、数据访问还是划分？ |
| 预处理、过滤、错标 | E06、E09、E10 | 被过滤的是损坏记录、无关背景还是任务相关信息？ |
| 开放集、低误报或漂移适应 | E07、E08、E22 | 阈值用了哪些目标域数据，负类分母与部署基率是什么？ |
| 多教师、LLM 流量分析或持续学习 | E11、E12 | 教师、验证标签、回放和测试数据的访问是否一致？ |
| trace/bag、多流或在线前缀 | E13–E17、E19、E22 | 当前时刻到底可见哪些包、流、方向与终止信息？ |
| 对抗鲁棒、隧道或指纹防御 | E16、E18–E20 | 扰动、攻击者知识、模拟/实装与观察点是否匹配？ |
| 工程复现、吞吐或网内部署 | E05、E06、E08、E18、E21 | 数字是否计入等待、解析、提特征、建图、更新和推理？ |

## 跨论文综合后应改变的做法

1. **按信息来源选择模型。** 单流、混合 trace、双侧关联、已知拓扑的边分类需要不同输入合同。Tracegram 的袋标签、GenETC 的全图信息和 Beyond RTT 的关联流，均不能直接替代单向外层流观测。先匹配任务与可见信号，再选择表示、模型和损失。
2. **把预训练价值拆成可比较的问题。** Sweet Danger 提供划分与冻结/微调控制，MM4flow 提供模态和标注效率证据，HF-Transformer 提供非预训练包头候选，GenETC 提供图信息增益及代价。它们没有在同一个协议上给出共同排名，不能据此宣告某个模型家族普遍优越或无效。
3. **把过滤当作可能改变任务的操作。** PacketLoom 展示格式验证和处理流程的收益，TraCare 展示删去相关流及阶段不一致的代价，FLOWREFINER+ 处理的是标签噪声。先保留各阶段样本计数、类别和来源，再决定是否过滤或纠错。
4. **将完整流性能与在线决策分开。** Swallow 的全时长归一化、Tracegram 的袋内双向上下文和 NestTor 的完整流表示都有明确前提。Holmes 的等待/拒绝与 CountMamba 的因果状态提供设计参考，但单个前缀上的精度不等于完整停止策略的误报水平。
5. **揭示适配和监督成本。** 目标负类校准、无标签适配、专家反馈、教师伪标签和完全零目标域访问分别报告。教师一致率、规则命中、袋内注意力不能直接充当独立逐流真值。
6. **让系统开销与科学主张对应。** 资源受限时同时比较简单统计/树模型、任务匹配的序列模型和所提机制。强浅层基线也要接受捷径审计；网络流水级、状态占用、等待和预处理成本不由参数量或 FLOPs 替代。

以上是条件化综合，不是新实验结果。单篇缺少某项细节首先是待核对项；只有它影响中心结论时才上升为实质问题，不按清单自动拒稿。

## 低基率与顺序检测：协议补充

以下是评估设计规则，不冒充上列论文均已完成的实验：

- 对固定决策单位、固定阈值，记目标占比为 π、TPR 为 r、FPR 为 f，则预期 precision 为 `πr / (πr + (1−π)f)`。换基率时明确 r/f 的条件分布是否仍适用；AUROC、OSCR 或平衡测试集精度不能替代部署工作点。
- 记录 FP 数、负类总数、独立 capture/主体/时间组数及阈值选择数据。零 FP 的固定阈值 IID 二项样本，一侧 95% 上界为 `1−0.05^(1/n)`；相关流、测试后选阈值或多次窥视不满足这一直接解释。
- 在线检测以统一候选 cohort 从首次可见事件开始回放。每个快照只能使用已到达信息；输出告警、继续观察或终止/超时。全体候选与达到给定前缀的子集分别报告；缺失真正到达时间时只能称 capture-time replay。
- 按完整停止策略统计每条负流“曾经告警”的比例，不能把每个前缀的 FPR 当成累计 FPR。延迟同时给出检出覆盖率与未检出/截尾数量；仅在成功样本上平均不能概括系统检出延迟。
- 检查窗口长度、归一化时长、图邻居、padding、流终止等是否隐含未来信息。当前已闭合窗口内的双向计算可以合法使用，但要计入窗口等待；它不等于可逐包复用状态的因果模型。

## 逐篇机制与来源


### E01 · SoK: Decoding the Enigma of Encrypted Network Traffic Classifiers

- **机制**：用 ET-BERT、YaTC 在两套 TLS 1.3 数据的抽选类别上做字段遮蔽，区分显式标识、上下文、时间字段和加密内容的影响。
- **条件与反证**：系统化梳理覆盖的分类器数量不等于遮蔽实验实际运行的模型数量；遮蔽结果属于选定数据和模型，不能推出所有表征学习无效。
- **转为 Skill 规则**：先记录协议、密码套件、粒度和遮蔽对照；按样本组划分，再区分可部署信号与采集环境捷径。
- **原文定位**：§5.3.1–5.3.3（解析稿 L376–L392）；§5.3.7–§6（解析稿 L452–L509）。源文件：`02-parsed-markdown/2025-S&P-SoK_Decoding_the_Enigma_of_Encrypted_Network_Traffic_Classifiers.md`。


### E02 · The Sweet Danger of Sugar: Debunking Representation Learning for Encrypted Traffic Classification

- **机制**：把划分单位与编码器是否冻结分开比较，并用包头手工特征的浅层模型检验表征的实际增益。
- **条件与反证**：per-flow 与 per-packet、冻结与微调同时改变时，差值不能全归于某一个因素；浅层模型同样可能利用显式或隐式流标识。
- **转为 Skill 规则**：涉及预训练贡献时比较随机初始化、冻结探测和完整微调；匹配输入与预算。冻结编码器表现差不是端到端模型无效的充分条件。
- **原文定位**：§4.1（解析稿 L300–L324）；§6.1（解析稿 L409–L437）；§6.1 header/shallow comparisons（解析稿 L480–L516）。源文件：`02-parsed-markdown/2025-SIGCOMM-The_Sweet_Danger_of_Sugar_Debunking_Representation_Learning_for_Encrypted_Traffic_Classification.md`。


### E03 · MM4flow: A Pre-trained Multi-modal Model for Versatile Network Traffic Analysis

- **机制**：分别预训练字节流与包长序列，再在下游微调时融合；作者说明直接跨模态注意力可能破坏单模态表示。
- **条件与反证**：§4.3 的泛化主要通过标注量变化衡量；Browser 任务极少标签时存在弱于部分基线的情况。不能把样本效率直接表述为跨环境不变性。
- **转为 Skill 规则**：将预训练收益、融合收益、标签效率和跨域迁移拆成独立问题；保留单模态结果、从头训练对照和融合成本。
- **原文定位**：§3.5（解析稿 L966–L982）；§4.1（解析稿 L1082–L1092）；§4.3（解析稿 L1121–L1129）；§5（解析稿 L1272–L1285）。源文件：`02-parsed-markdown/2025-CCS-MM4flow__A_Pre-trained_Multi-modal_Model_for_Versatile_Network_Traffic_Analysis.md`。


### E04 · HF-Transformer: A Non-Pretrained Encrypted Network Traffic Classification Model Based on Packet Header Fields

- **机制**：从少量包头字段训练非预训练 Transformer；所选字段包括 IP 长度、TTL 以及 TCP Seq、Ack、Window。
- **条件与反证**：可见包头不天然免于捷径：Seq/Ack 可能携带隐式流标识，TTL/Window 也可能反映来源环境。该机制与 E01/E02 的审计要求必须同时考虑。
- **转为 Skill 规则**：把 HF-Transformer 纳入非预训练包头路线候选，同时要求原始会话分组、Seq/Ack 重编码或消融、跨来源验证；不得仅凭其排名宣告预训练无用。
- **原文定位**：§II-C / §III（解析稿 L127–L150）；§IV（解析稿 L183–L219）。源文件：`02-parsed-markdown/2026-INFOCOM-HF-Transformer__A_Non-Pretrained_Encrypted_Network_Traffic_Classification_Model_Based_on_Packet_Header_Fields.md`。


### E05 · GenETC: Dynamic Graph-enhanced Fine-tuning for Encrypted Traffic Classification

- **机制**：用 IP/端口构建流交互图，流表示作为边属性，并在微调中更新语义与图特征；主设置明确使用全数据的传导式图。
- **条件与反证**：全图可见性与新端点归纳部署不同；作者承认端点不重叠时下降、Tor 场景图收益有限。耗时统计含图更新而不含初始建图。
- **转为 Skill 规则**：单独记录传导/归纳设定、测试节点和边可见性、是否接触未来流、端点标识、建图成本。声明传导式使用不等于标签泄漏，但不能外推在线归纳性能。
- **原文定位**：§IV-C（解析稿 L125–L131）；§V-A（解析稿 L284–L290）；§V-G / §VI（解析稿 L408–L424）。源文件：`02-parsed-markdown/2026-TDSC-GenETC__Dynamic_Graph-enhanced_Fine-tuning_for_Encrypted_Traffic_Classification.md`。


### E06 · PacketLoom: A Unified Preprocessing Framework for Multi-Modal Network Traffic Analysis

- **机制**：声明式配置将预处理定义与执行分开；惰性加载、减少中间磁盘读写改善迭代效率，并对 ET-BERT 与 YaTC 流程做复现比较。
- **条件与反证**：YaTC 计时只覆盖相同预切分输入的光栅化阶段；ET-BERT 比较覆盖不同处理范围，且过滤改变样本组成。单数据集的复现不能证明所有模态严格等价。
- **转为 Skill 规则**：记录解析、过滤、分组、采样、导出配置及样本保留率；分别评价语义一致性和工程速度。缓存、I/O、内存交换与计时边界必须对齐。
- **原文定位**：§II-A（解析稿 L59–L70）；§III-A/B（解析稿 L169–L187）；§III-D/E（解析稿 L211–L228）。源文件：`02-parsed-markdown/2026-INFOCOM-PacketLoom__A_Unified_Preprocessing_Framework_for_Multi-Modal_Network_Traffic_Analysis.md`。


### E07 · PP-OpenNet: Privacy-Preserved Open Set Classification for Network Traffic

- **机制**：从时间戳和包长构造多尺度特征，经 GRU 融合，使用原型相关的开放集判别；区分目标应用、训练背景与测试未知应用。
- **条件与反证**：测试未知样本占比为 50%；回溯切片可使用此前历史包；同一学习率用于所有模型不自动保证公平。无载荷输入也不是形式化隐私保证。
- **转为 Skill 规则**：同时报告已知类正确识别与未知拒绝指标（如 OSCR），核对 trace 隔离、历史包可用性、背景重叠和验证集调参预算；另做部署基率与固定阈值评估。
- **原文定位**：§III-A（解析稿 L50–L58）；§III-C/D / §IV-A/B（解析稿 L75–L101）；§IV-D（解析稿 L116–L128）。源文件：`02-parsed-markdown/2026-INFOCOM-PP-OpenNet__Privacy-Preserved_Open_Set_Classification_for_Network_Traffic.md`。


### E08 · DualGuard: An Adaptive Detection Framework for Concurrent Malicious and Normality Drifts in Network Traffic

- **机制**：区分恶意类别变化与正常背景变化；对漂移告警聚类、选代表样本交由专家标注，再传播标签并更新模型。
- **条件与反证**：ISP 验证采用提取后的流记录离线回放；经筛选的 Suricata/ET 命中是运营参考而非独立完整真值，ET 阴性仍可能含漏检。
- **转为 Skill 规则**：拆开良性与恶意漂移来源，计入专家标签、更新延迟和标注预算；明确规则覆盖及测试时是否适配。提取后吞吐不能充当链路实时吞吐。
- **原文定位**：§IV-A（解析稿 L123–L139）；§V-E / §VI-A（解析稿 L325–L347）；§VII（解析稿 L497–L511）。源文件：`02-parsed-markdown/2026-TIFS-DualGuard__An_Adaptive_Detection_Framework_for_Concurrent_Malicious_and_Normality_Drifts_in_Network_Traffic.md`。


### E09 · Is the Data Really Noisy? Rethinking Traffic Filtering for Encrypted Traffic Classification

- **机制**：研究过滤在开发/部署阶段、流量类别和策略三个维度的作用；TraCare 利用同活动流量的关联增强表示并采用免过滤推理。
- **条件与反证**：短流或辅助协议可能携带活动信息；训练与部署过滤不一致会改变分布。该结论不等于应保留损坏包，也不否定有任务依据的过滤。
- **转为 Skill 规则**：每个过滤步骤写明目的、作用阶段、被剔除标签/来源及覆盖代价；与 E06 对照时区分工程格式验证、无关流排除和语义相关流删除。
- **原文定位**：§III-A（解析稿 L80–L92）；§III-F（解析稿 L166–L184）；§IV-B（解析稿 L233–L241）。源文件：`02-parsed-markdown/2026-TDSC-Is_the_Data_Really_Noisy_Rethinking_Traffic_Filtering_for_Encrypted_Traffic_Classification.md`。


### E10 · Fighting the Enemy Within: Traffic Semantics-Driven Framework Against Noisy Labels for Robust Traffic Classification

- **机制**：FLOWREFINER+ 先用自监督表征检测可能错标的流，再基于置信度纠正标签，最后训练跨粒度分类器。
- **条件与反证**：实验同时区分合成标签噪声、类别相关噪声与真实错标数据。模型同意度或高置信度不是独立真值，表征中的来源偏差可能被纠错过程保留。
- **转为 Skill 规则**：将标签噪声、无关背景、观测扰动和分布漂移分开；保存纠正前后标签及依据，评价各类样本保留率和独立测试上的纠错效果。
- **原文定位**：§III-A（解析稿 L46–L54）；§III-B（解析稿 L100–L110）；§IV-A（解析稿 L165–L177）；§IV-C（解析稿 L261–L280）。源文件：`02-parsed-markdown/2026-TON-Fighting_the_Enemy_Within__Traffic_Semantics-Driven_Framework_Against_Noisy_Labels_for_Robust_Traffic_Classification.md`。


### E11 · Two Heads Are Better Than One: Models-to-Model Learning for Encrypted Traffic Analysis

- **机制**：M2ML 把异构教师特征文本化，结合教师的验证表现和样本级共识融合监督，由学生学习。
- **条件与反证**：学生阶段无需手工标签不表示整条流程没有标签成本；教师质量和验证标签仍是来源。文本化对原始字节顺序、包边界及深层表示可能丢失结构。
- **转为 Skill 规则**：分别记录教师训练、可信度估计与学生训练的数据访问；保留教师误差、冲突及校准分析，防止把教师一致预测当独立标签验证。
- **原文定位**：§III-B（解析稿 L77–L93）；§IV-C（解析稿 L214–L224）；§VI-A/B（解析稿 L478–L493）。源文件：`02-parsed-markdown/2026-TIFS-Two_Heads_Are_Better_Than_One__Models-to-Model_Learning_for_Encrypted_Traffic_Analysis.md`。


### E12 · Two Heads Are Not Better Than One: Continual Learning From Multiple Models for Encrypted Traffic Analysis

- **机制**：DBCL 从各任务教师逐步获取监督，利用快/慢 LoRA 分支、回放缓冲和知识对齐平衡学习与保留。
- **条件与反证**：教师噪声会降低学生效果，回放保留历史样本且有成本。它研究跨任务持续吸收，与 E11 的同一样本多教师仲裁不是标题上的直接反证关系。
- **转为 Skill 规则**：比较教师访问、任务顺序、回放预算、旧任务遗忘和新任务学习；知识保留同时对教师一致率与独立真值准确率报告。
- **原文定位**：§III-B（解析稿 L106–L128）；§III-C（解析稿 L168–L182）；§IV-E / §V（解析稿 L450–L505）。源文件：`02-parsed-markdown/2026-TIFS-Two_Heads_Are_Not_Better_Than_One__Continual_Learning_From_Multiple_Models_for_Encrypted_Traffic_Analysis.md`。


### E13 · Tracegram: Framing Trace-Level Traffic Analysis with Temporally-Aware Multiple Instance Learning

- **机制**：把 trace 视为 bag、流视为 instance，以 bag 标签监督；在流表征后通过 Bi-LSTM 与注意力聚合，保留跨流顺序。
- **条件与反证**：bag 标签不等于每条流真值；注意力是模型归因而非因果验证。Bi-LSTM 使用袋内后续流，完整 trace 结果不能自动外推到持续在线决策；随机干扰验证不等于自适应攻击鲁棒。
- **转为 Skill 规则**：先定义袋边界和可见流集合，再评价缺失流、背景注入与归因；在线迁移须声明当前窗口闭合或改用前缀可用的聚合。
- **原文定位**：§2.2（解析稿 L85–L91）；§3.2（解析稿 L113–L125）；§3.5（解析稿 L145–L153）；§6（解析稿 L397–L403）。源文件：`02-parsed-markdown/2026-USENIX-Tracegram__Framing_Trace-Level_Traffic_Analysis_with_Temporally-Aware_Multiple_Instance_Learning.md`。


### E14 · Robust and Reliable Early-Stage Website Fingerprinting Attacks via Spatial-Temporal Distribution Analysis

- **机制**：Holmes 定时尝试识别，将当前前缀映射到特征空间，与站点中心和半径比较；证据不足时继续收包。
- **条件与反证**：提前分类算法与按最终加载比例截断的离线曲线是不同证据。讨论中使用守卫节点 circuit ID 的扩展依赖更强观察能力。
- **转为 Skill 规则**：记录等待、拒绝、终止和最大时限；保留全体候选分母，报告完整停止策略的累计误报、检出率和延迟。外部被动观察者不能默认获得 circuit ID。
- **原文定位**：§5.3 / Algorithm 2（解析稿 L404–L435）；§6.3（解析稿 L546–L552）；§7（解析稿 L625–L637）。源文件：`02-parsed-markdown/2024-CCS-Robust_and_reliable_early-stage_website_fingerprinting_attacks_via_spatial-temporal_distribution_analysis.md`。


### E15 · COUNTMAMBA: A Generalized Website Fingerprinting Attack via Coarse-Grained Representation and Fine-Grained Prediction

- **机制**：以时间窗计数表示流量，对各窗施加监督并聚合已到达窗口的预测；因果卷积与状态空间模块支持状态复用。
- **条件与反证**：加载比例曲线仍依赖事后完整 trace 时长；状态可复用是计算性质，不自动证明在线停止规则校准或乱序到达下有效。
- **转为 Skill 规则**：区分模型因果性、增量状态复用与停止决策三项；核对归一化、窗口边界和时间戳语义是否仅使用决策时已见信息。
- **原文定位**：§5.2（解析稿 L455–L474）；§6.3（解析稿 L601–L613）；Appendix E（解析稿 L902–L908）。源文件：`02-parsed-markdown/2025-S&P-Countmamba__A_Generalized_Website_Fingerprinting_Attack_via_Coarse-Grained_Representation_and_Fine-Grained_Prediction.md`。


### E16 · Swallow: A Transfer-Robust Website Fingerprinting Attack via Consistent Feature Learning

- **机制**：CIF 依据完整 trace 加载时间调整计数区间，结合增强与少样本训练提升跨网络条件迁移。
- **条件与反证**：按全时长构造的表示在流终止前不可直接取得；防御评估使用模拟变换，且作者承认 Palette 等防御下仍有限。实验聚焦单标签页主页。
- **转为 Skill 规则**：把完整 trace 迁移作为离线证据；前缀迁移需换用已观测时间或独立时长估计。分开报告模拟/实装防御、主页/子页、单/多标签页。
- **原文定位**：§3（解析稿 L193–L195）；§5.1（解析稿 L406–L412）；§6.3（解析稿 L636–L646）；§7（解析稿 L700–L708）。源文件：`02-parsed-markdown/2025-CCS-Swallow__A_Transfer-Robust_Website_Fingerprinting_Attack_via_Consistent_Feature_Learning.md`。


### E17 · Towards Practical Few-shot Multi-tab Website Fingerprinting

- **机制**：MMF 以支持集驱动的特征重加权和目标存在性判别处理混合标签页，并支持少量新站点样本适配。
- **条件与反证**：P@k、未知标签页数下的存在性判断、开放世界误报对应不同量；作者说明仍缺少人类参与采集的完整端到端基准。合成混合与真实并发不是同一个证据源。
- **转为 Skill 规则**：记录基础/新类别、每类 shots、混合方式、并发数量和时间交叠；报告新类学习、旧类遗忘、集合扩张成本及目标存在性误报。
- **原文定位**：§3（解析稿 L99–L105）；§5.3（解析稿 L312–L318）；§5.7（解析稿 L385–L391）；§6（解析稿 L452–L458）。源文件：`02-parsed-markdown/2026-USENIX-Towards_Practical_Few-shot_Multi-tab_Website_Fingerprinting.md`。


### E18 · CertTA: Certified Robustness Made Practical for Learning-Based Traffic Analysis

- **机制**：通过抽样包并对长度和间隔加噪生成平滑样本，聚合基分类器预测，推导特定离散与加性扰动区域内的鲁棒性保证。
- **条件与反证**：保证绑定平滑分类器、扰动定义和统计置信条件；区域外攻击、协议语义改变、未知背景或真实线路性能不由该证书自动涵盖。
- **转为 Skill 规则**：认证准确率、经验逃逸率、干净样本精度与拒绝率分别报告；说明扰动预算、平滑样本数、置信水平及平滑推理的总成本。
- **原文定位**：§3/4（解析稿 L148–L154）；§5.1（解析稿 L493–L508）；§6（解析稿 L597–L605）。源文件：`02-parsed-markdown/2025-USENIX-CertTA__Certified_Robustness_Made_Practical_for_Learning-Based_Traffic_Analysis.md`。


### E19 · Beyond RTT: An Adversarially Robust Two-Tiered Approach for Residential Proxy Detection

- **机制**：轻量特征与 CorrTransform 构成互补检测路线，利用代理网关流与转发流的关联抵抗部分时间扰动。
- **条件与反证**：ISP 与内容服务商的可见集合不同；关联检测需要相应流的观察权限。采集集中于一个提供商，其他提供商上的实证验证留作后续。
- **转为 Skill 规则**：先核对单观察点是否拥有所需两侧或多流证据；固定各模型前缀与候选关联范围，避免把多视角检测能力移植成单向外层检测结论。
- **原文定位**：§III（解析稿 L72–L82）；§VI-A（解析稿 L177–L183）；§VI-B（解析稿 L227–L233）；§VIII（解析稿 L329–L333）。源文件：`02-parsed-markdown/2026-NDSS-Beyond_RTT__An_Adversarially_Robust_Two-Tiered_Approach_for_Residential_Proxy_Detection.md`。


### E20 · Huma: Censorship Circumvention via Web Protocol Tunneling with Deferred Traffic Replacement

- **机制**：通过 Web 协议隧道和延迟内容替换承载通信，并在指定 DW/目标页面配置下比较正常访问与 Huma 访问。
- **条件与反证**：有限地点、页面大小配置和检测器下难区分属于特定实验；目标页更大时需额外抓取，不可直接推出任意用户活动分布下不可检测。
- **转为 Skill 规则**：评估隧道或防御时对齐页面、活动、路由及负类来源，检查自适应观察者；保持吞吐、时延、带宽与可检测性联合权衡。
- **原文定位**：§III-A（解析稿 L105–L109）；§IV-B（解析稿 L228–L234）；§V-D（解析稿 L398–L404）。源文件：`02-parsed-markdown/2026-NDSS-Huma__Censorship_Circumvention_via_Web_Protocol_Tunneling_with_Deferred_Traffic_Replacement.md`。


### E21 · SketchFeature: High-Quality Per-Flow Feature Extractor Towards Security-Aware Data Plane

- **机制**：量化、虚拟 sketch 与成员测试共同提取逐流分布特征，成员测试用于抑制虚假解码。
- **条件与反证**：数据平面特征提取不代表所有后续分类也在数据平面；作者讨论现有网内模型与三阶特征的兼容限制，特征质量还受内存和负载影响。
- **转为 Skill 规则**：分别测试特征恢复误差与下游检测损失，报告 hash 冲突、量化粒度、并发流、SRAM/流水级与模型位置。
- **原文定位**：§III-B（解析稿 L98–L102）；§III-C（解析稿 L146–L152）；§V（解析稿 L426–L435）；§VI（解析稿 L533–L537）。源文件：`02-parsed-markdown/2025-NDSS-SketchFeature__High-Quality_Per-Flow_Feature_Extractor_Towards_Security-Aware_Data_Plane.md`。


### E22 · NESTTOR: Detecting Tor-over-Proxy Traffic with Factorized Decisions

- **机制**：将 ToP 定义为代理存在与 Tor 存在的合取；独立训练代理门控和代理条件下的 Tor 检测器，通过 AND 决策。
- **条件与反证**：目标代理的独立 Pure Proxy 负类可参与校准，因此与完全不接触目标域不同；负类分位数校准修正位置/尺度，不恢复排序。论文适用完整流离线处理。
- **转为 Skill 规则**：保留四类标签真值表，区分条件检测与端到端系统；与同表示、同分类器族的平面结构比较。分别申明目标负类、无标签目标数据与目标正类访问。
- **原文定位**：§III-A（解析稿 L178–L196）；§IV-B（解析稿 L306–L320）；§IV-C（解析稿 L356–L372）；§VI-E（解析稿 L614–L616）。源文件：`02-parsed-markdown/2026-myPaper-TNSM-NestTor_Detecting_Tor-over-Proxy_Traffic_with_Factorized_Decisions.md`。


## 后续增量维护

新增论文先检查与这些机制是否真正不同，再追加或修订对应证据记录。记录原文定位、适用条件、反证以及将改变的具体决策；相同数据、共享教师或同一版本扩展不累计为独立共识。发现旧规则与新证据冲突时，先对齐任务、划分、数据访问及指标，确认是直接反证还是条件变化。不要按 venue、年代或引用数自动升级证据。

用户只要求写作或概念分析时，这些规则用于校准现有论证，不自动启动实验。用户要求评估或实验设计时，按中心主张选择最小的区分性对照，不能把全部条目都变成必做实验。
