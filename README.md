# paper_notes


model editing:
- https://blog.csdn.net/m0_59235245/article/details/149206534
- https://zhuanlan.zhihu.com/p/609177437

red team testing:
- https://www.confident-ai.com/blog/red-teaming-llms-a-step-by-step-guide 
    (zh: https://www.testwo.com/article/2200)
- https://huggingface.co/blog/zh/red-teaming 
    （红队语言模型的目标是制作一个提示 (prompt)，该提示会触发模型生成有害内容。红队和同样知名的评估语言模型 对抗攻击 有同也有异。相似之处在于红队和对抗攻击目标相同，即“攻击”或“欺骗”模型，以生成在现实世界中不想要的内容。但是对抗攻击很难让人理解，举例来说，通过将字符串 “aaabbbcc” 前缀到每个提示中，它会恶化模型性能。Wallace 等人 2019 年的论文 讨论了对各种 NLP 分类和生成任务的许多攻击的例子。在另一方面，红队的提示看起来更正常，像自然语言的提示。）
- 未来方向:
    - 没有用于代码生成的开源红队数据集，它试图通过代码越狱模型，例如生成实现 DDOS 或后门攻击的程序。
    - 为关键威胁场景设计和实施大语言模型红队方案的策略。
    - 红队可能是资源密集的，无论是计算还是人力资源，因此将从共享策略，开源数据集以及可能的合作中获得更大的成功机会，从而受益。
    - 评估回避和有帮助之间的权衡。
    - 综合比较根据上述方案的利弊，找到红队方案的最优解集 (类似于 Anthropic 的 Constitutional AI)。

一点思考：
- red-teaming 和 jailbreak 的区别：red-teaming 最核心的还是用来做测试，既然是测试就需要能够从 evaluation 中看出来模型有哪些不足，比如在多语言的情况下容易漏洞，在被用作 agent 的时候，或者被用来生成虚假信息的时候...是有具体的应用场景和领域；jailbreak 主要是攻击，只要引导模型输出有害输出就可以了，只要是它不该输出的都可以，比如怎么抢劫银行，没有那么 domain-specific
- 但是好像 jailbreak 是 red-teaming 的一个子类。。