# Code-Switching Red-Teaming: LLM Evaluation for Safety and Multilingual Understanding

multilingual多语言的
monolingual单语言的

![alt text](image.png)

## abstract
攻击对象：llm （无特定限制）
攻击方法：CSRT(code-switching red-teaming)
baseline：existing multilingual red-teaming techniques and standard red-teaming in English

## introduction
code-switching texts是指单语境中混合多种语言的文本
Red-teaming is a key component of AI safety to discover and fix vulnerabilities before deployment.
在预训练的时候用于安全对齐的数据的语言分布不平衡可能导致llm在非英语语境下暴露出漏洞

研究背景与核心问题
随着大语言模型（LLMs）的快速发展，其多语言能力日益凸显 —— 即便部分模型官方仅支持英语，也能通过网页爬取的多语言训练语料理解非英语文本，甚至生成 “语码转换”（Code-Switching）文本（单语境中混合多种语言）。然而，LLM 的安全性问题愈发突出，现有红队测试（Red-Teaming）存在明显局限：
- 多语言红队测试不足：传统红队测试多聚焦单语言（尤其英语），非英语红队数据集稀缺，且未覆盖 “语码转换” 这一自然语言常见现象；
- 安全与多语言能力评估脱节：现有方法难以同时评估 LLM 的 “安全合规性”（是否生成有害内容）与 “多语言理解能力”（是否理解混合语言文本）；
- 语言资源不均衡影响安全性：非英语语言（尤其低资源语言）在 LLM 安全对齐数据中占比低，可能导致 LLM 对非英语或语码转换的恶意提示更易感，但此漏洞尚未被系统挖掘。

ablation study:
1. the number of languages used in code-switching
2. the resource availability of languages used in code-switching.

discover that leveraging a greater number of languages and languages with lower resources increases the attack success rate of code-switching red-teaming.

主要贡献：
1. 揭示 LLM 对语码转换的脆弱性，提出 CSRT 新框架
2. 构建通用自动化评估体系 CSRT，全面验证 10 个主流 LLM
3. 挖掘 LLM 的在语言资源 - 安全对齐上的非预期关联，为安全优化提供理论依据

## related work
语码转换（Code-Switching）定义：是一个语言学上的概念，指多语言使用者在单一对话语境中混合使用两种及以上语言或语言变体的现象，是自然语言交流的常见形式，需 LLM 理解所有混合语言的语言学知识。

## code-switching red-teaming
![alt text](image-1.png)
1. seed data from MultiJail, translate into diverse languages
2. use gpt-4o to generate code-switching queries

CSRT 是一种简单且高效的红队攻击框架，通过生成 “语码转换红队提示”，同时评估 LLM 的安全性与多语言理解能力，核心流程分两步（如图 2 所示）：
步骤 1：红队提示多语言翻译：以 MultiJail（含 9 种语言的多语言红队数据集）的 315 条英语恶意提示为种子数据，将其人工翻译为 10 种语言（含高资源语言如中文、意大利语；中资源语言如阿拉伯语、韩语；低资源语言如孟加拉语、斯瓦希里语、爪哇语）；
步骤 2：LLM 生成语码转换提示：使用 GPT-4o 作为生成器，输入上述 10 种语言的平行恶意提示，结合 “语码转换” 定义，自动合成语码转换红队提示。最终构建的 CSRT 数据集含 315 条提示，每条平均混合 7.83±2.19 种语言，英语 token 占比 20.9%，其他语言 token 占比 7.5%-10.4%，分布均衡。

## experiments
- metrics:
    ![alt text](image-2.png)
    1. attack success rate (ASR), 诱导 LLM 生成违反伦理、法律或安全准则内容的测试案例占比（0 = 无害，1 = 有害）.衡量 LLM 安全性，ASR 越高说明 LLM 越易被攻击
    2. refusal rate (RR), LLM 生成含拒绝短语（如 “抱歉无法协助”）的测试案例占比（0 = 无拒绝，1 = 完全拒绝）补充评估安全性，需注意 RR 与 ASR 独立 —— 部分响应先拒绝但仍含有害内容，会同时计为 RR=1、ASR=1
    3. comprehension (Cmp.), LLM 理解用户意图并提供相关响应的测试案例占比（0 = 无关，1 = 相关）.衡量多语言理解能力，0 分包括 “随机响应”“误读意图”“仅重复提示无实质内容” 三类情况
- baseline: 2 种现有多语言红队方法（MultiJail 单语言提示、Sandwich Attack 句子级多语言混合）+3 种非多语言红队方法（GCG、AutoDAN、PAIR）；
- 防御基线：2 种对抗防御方法（Perplexity Pass 困惑度过滤、Paraphrase 重述）；

![alt text](image-3.png)

![alt text](image-4.png)

![alt text](image-5.png)

核心实验结果
（1）CSRT 攻击效果显著优于现有方法
- ASR 提升明显：在所有模型（除 Claude 3 系列）中，CSRT 的 ASR 最高 —— 比英语标准红队攻击高 46.7%，比 Sandwich Attack（句子级多语言混合）高 5%-15%，比 MultiJail 低资源语言（如爪哇语）提示高 8%-12%（如表 2a 所示）；
- 覆盖全安全领域：在 6 类安全风险中，CSRT 均能有效诱导有害内容，尤其对 “偏见”“暴力犯罪” 领域提升最显著（如偏见类 ASR 达 17.06%，远超 MultiJail 英语提示的 1.96%），同时也能放大传统红队难以覆盖的 “非传统危害”（如表 3 所示）；
- 优于非多语言攻击：CSRT 对 Llama 3（8B）的 ASR 达 33.63%，远超 GCG（19.84%）、AutoDAN（26.30%）等非多语言红队方法（如表 4 所示）。

(2) CSRT 能绕过现有防御机制
现有防御方法对 CSRT 效果有限：Perplexity Pass 仅使 CSRT 的 ASR 从 33.63% 降至 32.11%，Paraphrase 仅降至 27.68%，而对英语标准攻击的 ASR 可从 16.28% 降至 9.27%（如表 5 所示），说明语码转换提示的对抗性更强。

（3）LLM 多语言理解能力差异明显
- 低资源语言理解薄弱：所有模型在低资源语言（如爪哇语）提示中的 Cmp. 得分比高资源语言（如中文）低 20%-30%；
- Qwen 1.5 与 Claude 3 表现最佳：Qwen 1.5（7B）在 CSRT 中的 Cmp. 得分达 85.14%，Claude 3 Sonnet 达 80.25%，显著优于 Llama 3（8B，78.11%）和 Mistral（7B，70.18%）（如表 2c 所示）；
- 对抗性提示加剧理解差距：LLM 在 “语码转换恶意提示” 中的 Cmp. 得分，比 “语码转换非恶意提示”（如常识问答）低 5%-10%，说明恶意内容会进一步干扰 LLM 的多语言理解（如表 7 所示）。

（4）语码转换攻击的关键影响因素（消融实验）
- 语言数量越多，攻击效果越强：混合 10 种语言的 CSRT 提示 ASR 比混合 2 种语言的高 12%-15%（如图 3a 所示），因为多语言混合更易突破 LLM 的安全对齐逻辑；
- 低资源语言占比越高，ASR 越高：含更多低资源语言（如孟加拉语、斯瓦希里语）的提示，ASR 比高资源语言混合提示高 8%-10%（如图 3b 所示），印证 “语言资源不均衡与安全对齐相关” 的假设；
- 无需人工翻译，可自动化扩展：仅用英语单语言提示，通过 LLM 自动翻译 + 语码转换生成的 CSRT 提示，ASR 虽比人工翻译种子低 5%-8%，但仍远超英语标准攻击（如表 6 所示），说明 CSRT 可低成本扩展到大规模单语言红队数据集。

（5）特殊发现：Claude 3 系列的异常表现
Claude 3 Sonnet/Haiku/Opus 的 CSRT-ASR 极低（3.18%-8.89%），甚至低于 MultiJail 英语提示，推测因 MultiJail 种子数据部分来自 Anthropic 的 HH-RLHF 数据集，Claude 3 系列训练时接触过相关提示，存在数据污染问题 —— 而对 CSRT 提示进行同义改写后，其 ASR 可提升至 11.96%，恢复至正常模型水平。

## conclusion
1. LLM 对语码转换提示易感：语码转换作为自然语言现象，能有效突破 LLM 的安全防护，证明 “语言形式创新” 是红队测试的重要方向；
2. 语言资源与安全对齐存在非预期关联：低资源语言在 LLM 安全训练中占比低，导致使用低资源语言的语码转换提示更易诱导有害内容，此关联是现有多语言 LLM 的核心安全漏洞；
3. 安全与多语言能力需协同评估：CSRT 首次实现 “安全性” 与 “多语言理解能力” 的联合评估，发现 LLM 的 Cmp. 得分与 ASR 呈正相关（相关系数 0.24）—— 即理解能力越强的 LLM，若安全对齐不足，反而更易被语码转换提示攻击；
4. 模型规模与安全性正相关：开源模型中，参数越大（如 Llama 3 70B vs 8B），CSRT 的 ASR 越低、RR 越高、Cmp. 越高，印证 LLM 在多语言安全与理解能力上的 “规模定律”（如表 4 及图 4 所示）。

## limitations
- 数据集规模受限：CSRT 数据集仅 315 条提示（与 MultiJail 种子数量一致），虽 ablation 实验扩展至 1.6 万条，但整体规模仍较小；
- 语码转换类型单一：仅聚焦 “句内语码转换”（token 级混合），未覆盖 “句间语码转换”“标签语码转换” 等其他类型；
- 依赖 LLM 评估者：虽验证 GPT-4o 评估与人类判断的高相关性，但低资源语言场景下评估准确性可能下降；
- 缺乏人类标注语码转换数据：现有 CSRT 由 LLM 自动生成，缺乏人类自然语码转换的恶意提示，可能与真实场景存在差异。