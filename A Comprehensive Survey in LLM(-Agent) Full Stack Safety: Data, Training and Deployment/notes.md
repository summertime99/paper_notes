# A Comprehensive Survey in LLM(-Agent) Full Stack Safety: Data, Training and Deployment

https://hub.baai.ac.cn/view/45238

## abstract

contribution: 站在 llm 的 lifechain 的角度全面系统性地总结了 llm 的 safety issues，rather than focus on specific stages like deployment or fine-tuning phases

LLM lifecycle: data preparation, pre-training, post-training (including alignment and fine-tuning, model editing, etc.), deployment and final commercialization.

promising research directions: safety in data generation, alignment techniques, model editing, and LLM-based agent systems. 

## introduction

data preparation: 需要大量数据，而很多数据都是从互联网或者其他开源场景来的，其中的有毒性和用户隐私可能渗漏到参数中，引发crises。

pretraining: 非监督的，可能无意识地吸收到有毒数据和隐私信息，使得模型自带危险特性和隐私问题

fine-tuning:

deployment: jailbreak



### contribution

1. llm 安全应该包含以下方面：data preparation, model pre-training, posttraining, deployment, and finally usage. 进一步还可以将 post training 分成 alignment 和 fine-tuning。进一步包含了 model editing 和 unlearning 这些更新模型的knowledge或参数的方法。在 deployment 阶段还针对 pure llm models 和 llm-based agent 分别进行了讨论。
2. 强调了 llm safety 看起来比较有希望的研究方向和技术路线。



涵盖的内容：

模型参数固定前：data, pre-training(data filtering and augmenting), post-training(fine-tuning and alignment, and safety recovery after model safety breaches), dynamic updates in real-world scenarios(model editing and unlearning)

模型参数固定后：从 attack，defense和evaluation角度来讲，还进一步研究了 agent 的 safety（主要是分析了与 llm 相连接的外部 module 的机制）；提出了针对 llm-based applications 的商业化，道德 guidelines 以及 user usage。



## data safety

analyze **critical security risks and mitigation strategies** across four lifecycle phases of LLMs

### pre-training data safety

- training data poisoning：powerful and difficult to detect，0.1%的投毒数据可能对模型行为造成持久影响，even after extensive fine-tuning。并且修改网上的公开数据（比如Wikipedia）可能对现在及后续的训练造成持久影响。等等。
- privacy leakage：模型的泛化和能力越强，无意识地从训练数据中捕捉或者泄露 personal identifiable information(PII) 的风险越高。文献表明模型可以记住这些敏感信息并且通过定向攻击之后能够复现这些信息。
- 

### fine-tuning data safety



### alignment data safety



### secure and reliable data generation