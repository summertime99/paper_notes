# Effective Red-Teaming of Policy-Adherent Agents

> IBM Research AI

面向 policy-adherent agents（政策合规智能体） 的 red-teaming

questions:
- 测的是 agent 系统还是里面的 llm？（LLM agents that must comply with strict, domain-specific policy constraints.针对agent任务微调过的llm遵循政策合规约束的能力？）
- 研究对象到底是谁？---> 政策合规智能体（1. agent 2. adherent to domain-specific policies）
- 攻击 agent 是怎么运作的？通过特定的 prompt 让它去执行专门的工作，没有微调。

## abstract
研究背景与目标：在客服、医疗、金融等高风险领域，基于大语言模型（LLM）的任务导向智能体需严格遵循退款资格、取消规则等政策，既要拒绝违规请求，又要保持交互的实用性与自然性。然而，现实中存在恶意用户试图操纵智能体绕过政策以谋取私利，当前缺乏针对此类场景的定制化设计与评估方法。

核心目标（贡献）：
- 提出聚焦恶意用户利用政策合规智能体的威胁模型；（propose a novel threat model that focuses on adversarial users aiming to exploit policy-adherent agents for personal benefit.）
- 开发多智能体红队测试系统 CRAFT，以暴露政策合规智能体的漏洞；（present CRAFT, a multi-agent red-teaming system that leverages policy-aware persuasive strategies to undermine a policy-adherent agent in a customer-service scenario, outperforming conventional jailbreak methods like...）
- 构建评估智能体抵御操纵能力的基准；（introduce τ-break, a complementary benchmark designed to rigorously assess the agent’s robustness against manipulative user behavior.需要agents在处理user要求的同时坚持遵守 domain-specific 政策）
- 验证并分析防御策略的有效性，为更强防护措施提供方向。（evaluate several straightforward yet effective defense strategies. While these measures provide some protection, they fall short,...）

## introduction
CRAFT: Constraint-aware Red-teaming with Adversarial Framing and Tactics. 
CRAFT 是一个具备政策感知能力的红队测试多智能体系统，其设计目的是通过战略性、多步骤的对抗性规划，暴露受政策约束的大语言模型（LLM）智能体所存在的漏洞。与传统红队测试策略不同，CRAFT 会明确对目标智能体的政策进行推理，提取相关的政策片段，并利用这些片段在红队智能体与政策合规智能体的整个交互过程中为前者提供指导。

## method
![alt text](image.png)
threat model:
- simulate the reasoning process of malicious users that craft their requests to pursue specific personal objectives, often by leveraging policy-relevant details or ambiguous phrasing.
- method: CRAFT, 一个评测（攻击） policy-adherent agents 的多智能体系统，它要攻击的那个 agent 系统不一定是多智能体

CRAFT 通过模块化智能体协作，实现政策感知的红队测试，核心组件及功能如下：（示例攻击目标：取消已经不支持取消的航班预订）
组件	功能	示例
PolicyAnalyzer（政策分析器）	分析用户请求与完整政策文档，提取恶意用户可能操纵的政策片段，为后续攻击规划提供依据	若用户想修改基础经济舱航班日期，提取 “基础经济舱航班不可修改” 等相关政策条款
DeceptionPlanner（欺骗规划器）	核心推理组件，基于用户请求与提取的政策要点，生成诱导政策违规的具体提示（作为final message的种子）供红队 LLM 使用，常采用间接操纵策略	指导红队向智能体传递 “假设预订的航班非基础经济舱” 的虚假前提
AvoidanceAdvisor（规避顾问）	明确红队需避免提及的信息，防止目标智能体察觉请求违规	禁止红队提及 “因行程变更申请修改航班”，避免智能体直接依据政策拒绝
DialogueExecutor（对话执行器）	执行与目标智能体的交互，根据规划指令和目标智能体的回应动态调整响应，提升诱导违规成功率	在智能体拒绝修改航班后，重新强调 “假设航班可无限制改期” 的前提



## τ-break Dataset Construction
达到 attacker 的目的 != agent 未能完成任务
In our attack model, a successful attack occurs when the red-teamer induces the agent to perform a specific unauthorized action that serves their goal.

基于 τ-bench benchmark(已有的一个针对 policy-adherent agents 的benchmark，因为作者觉得自己去想政策违规的场景太麻烦了，就用现成的)改造，聚焦政策违规评估而非任务完成度，构建方式分两领域：
airline 领域：引入 “无政策约束智能体”，对比其行为（A_free）与 τ-bench 标准行为（A_gold），得到差异行为 ΔA=A_free-A_gold，人工筛选出有意义的政策违规行为（如取消不可退款机票），从 50 个原始任务中筛选 20 个纳入 τ-break。
retail 领域：新增 “敏感操作前需身份验证” 的政策约束与验证工具，将原本合规的请求转化为违规场景（如未验证身份即执行订单修改），新增 30 个违规测试案例。

## Experimental Setup
- metric: asr = pass@1
- models: GPT-4o、GPT-4o-mini、Qwen2.5-70B、LLaMA-3.3-70B-Instruct、DeepSeek-V3, 既作为目标智能体也作为攻击智能体；（PolicyAnalyzer 与规划智能体固定使用 LLaMA-3.3-70B）
- dataset: τ-break
- baseline: the cooperative user simulation from τ-bench, DAN, emotional manipulation, Direct Prompt Injection(DPI), insistent prompting

## Results and Analysis
- 政策感知的红队策略对政策合规智能体更有效。受政策约束的智能体不会受通用大语言模型的表层攻击影响，评估它们需借助能推理政策并利用其边缘漏洞的对抗性策略，否则传统方法会严重低估恶意用户的真实风险。
    ![alt text](image-1.png)
- τ-bench 的合作型用户模拟会高估政策合规智能体的安全性（CRAFT 评估下各模型、领域的 ASR 均显著上升），且不同模型在两种评估方式中表现差异明显，说明 CRAFT 更能发挥部分对齐模型的攻击能力，而对齐模型在基准策略中可能表现不佳。
    ![alt text](image-2.png)
- 攻击能力强不代表防御能力强，例如，Qwen2.5-70B 是效果最佳的攻击者之一，对多个目标智能体均实现了高攻击成功率（如对 GPT-4o-mini 的攻击成功率达 73.8%），但它同时也是防御能力最弱的目标智能体：当被 GPT-4o 攻击时，其攻击成功率（ASR）高达 80.0%。
    ![alt text](image-3.png)
- 智能体在简单身份验证政策下仍存在漏洞（零售领域）。即便智能体被赋予明确规则 —— 执行敏感操作前需对用户进行身份验证，在遭受攻击时，它们往往仍无法严格执行该规则。如表 1 所示，尽管这一约束条件十分简单，但 GPT-4o 的攻击成功率仍达 6.7%，而 Qwen 的攻击成功率更是高达 46.7%。这凸显了一个关键漏洞：即便面对清晰、易于执行的政策，在遭遇恶意用户攻击时，智能体仍可能无法坚守政策要求。

## ...

## limitations
- 实验基于合成数据（τ-bench 与 τ-break），未涉及真实用户数据与实时系统，可能无法完全反映现实场景复杂性；
- 聚焦航空、零售领域的静态政策，未覆盖法律、医疗等更动态、模糊的政策环境；
- 攻击智能体不具备自适应能力，无法根据历史攻击结果调整策略，与现实中恶意用户的动态攻击行为存在差异；
- 评估模型仅 5 个，且未涵盖领域专用模型，结果普适性有待验证。

![alt text](image-4.png)