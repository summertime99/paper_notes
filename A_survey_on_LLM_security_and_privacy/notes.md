# A survey on large language model (LLM) security and privacy: The Good, The Bad, and The Ugly

## abstract

llm 能力强大，在搜索引擎/用户支持/翻译等多个领域都有使用价值。在安全领域针对 llm 有两个研究方向：llm 自身的 security vulnerabilities，llm 在 security-related tasks 上的应用。

这篇文章针对 llm 在安全和隐私方面做了一个文献综述，分成三部分：good（有益的llm应用），bad（有害应用），ugly（llm的vulnerabilities and defenses）(Specifically, we investigate how LLMs positively impact security and privacy, potential risks and threats associated with their use, and inherent vulnerrabilities within LLMs. Through a comprehensive literature review, the paper categorizes the papersinto "The Good" (beneficial LLM applications), "The Bad" (offensive applications), and "The Ugly(vulnerabilities of LLMs and their defenses))

findings:
- good: enhance code security(code vulnerability detection) and data privacy(data confidentiality protection), outperforming traditional methods
- bad:  be harnessed for variout attacks (particularly user-level attacks) due tto their human-like reasoning abilities

## introduction
RQ:
1. How do LLMs make a positive impact on security and privacy across diverse domains, and what advantages do they offer to the security community?
2. What potential risks and threats emerge from the utilization of LLMs within the realm of cybersecurity?
3. What vulnerabilities and weaknesses within LLMs, and how to defend against those threats?

Fidings:
把文献分成三组：
- the good: high the security-beneficial applications
    - LLM have made contributions to both code security and data security and privacy.
    - code security: LLMs have been used for the whole lifecycle of the code(e.g.,secure coding,testcase generation,vulnerable code detection, malicious code detection, and code fixing).
    - data security and privacy: LLMs have been applied to ensure data integrity, data confidentiality, data reliability, and data traceability.
- the bad: explore applications that could potentially exert adverse impacts on security
    - five groups of attacks:
        - hardware-level attacks(e.g.,side-channel attacks)
        - OS-level attacks(e.g.,analyzing information from operating systems)
        - software-level attacks(e.g.,creating malware)
        - network-level attacks(e.g.,network phishing)
        - user-level attacks(e.g.,misinformation, social engineering(threaten privacy), scientific misconduct)
    - most prevalent(普遍): user-level attacks due to LLM's human-like reasoning abilities
    - os/hardward level 现在不用担心，一旦 LLM 获取 access to os/hardward level functions 就有危险了
- the ugly: focus on the discussion of security vulnerabilities (alongside potential defense mechanisms) within LLMs
    - two main groups of vulnerabilities in LLMs:
        - AI Model Inherent Vulnerabilities(e.g., data poisoning, backdoor attacks, training data extraction(threaten privacy))
        - Non-AI Model Inherent Vulnerabilities(e.g., remote code execution, prompt injection, side channels)
    - defenses divided into three groups according to where strategies are placed:
        - architecture: 
        - training phase: corpora cleaning, optimization methods
        - inference phase: instruction pre-processing, malicious detection, generation post-processing.

contributions:
- LLMs contribute more positively than negatively to security and privacy.(是指研究 good applications 的人比 bad 的多。。)
- most researchers concur that LLMs outperform state-of-the-art methods when employed for securing code or data.


