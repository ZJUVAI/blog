---
title: "LLM Comparator: Interactive Analysis of Side-by-Side Evaluation of Large Language Models "
tags: ["论文评述", "报告"]
date: 2024-09-19
author: 田昊宇
mathjax: true
---

论文：LLM Comparator: Interactive Analysis of Side-by-Side Evaluation of Large Language Models

作者：Minsuk Kahng, Ian Tenney, Mahima Pushkarna, Michael Xieyang Liu, James Wexler, Emily Reif, Krystal Kallarackal, Minsuk Chang, Michael Terry, Lucas Dixon

发表：VIS 2024/CHI EA'24

自动化并排(side-by-side)评估是一种有前景的研究方法，用于评估大语言模型的响应质量。然而，要对该方法的结果进行分析面对着可扩展性和可解释性的挑战。本文作者设计了 LLM Comparator——一种新的可视化分析工具，用于交互式分析自动化并排评估结果。该工具支持用户交互式工作，帮助他们理解模型在何时以及为何优于或劣于基准模型，并分析两个模型生成的响应在质量上的差异。作者与一家大型科技公司的研究人员和工程师紧密合作、迭代设计和开发了该工具。本文详细描述了作者识别到的用户挑战、工具的设计和开发过程，以及对定期评估其模型的实验人员进行的观察性研究。

[论文链接](https://dl.acm.org/doi/abs/10.1145/3613905.3650755)

## 背景介绍

评估一个生成式人工智能模型，需要评估它们的输出。很直接的想法是分析大模型在定量数据集上的表现（比如预测天气或者是预测玉米的价格），因为我们可以很直观地确定大模型输出的准确性。但是在实际情况中，用户的输入并不是总能有一个标准答案，所以很难为这类开放式任务设置 ground truth 用于验证大模型表现。在大模型的得分表现和人类的喜好程度上存在着一定的 gap，并不一定总体得分更高的大模型生成的内容就更受人类喜欢。这就是作者提出**LLM Comparator**的原因，旨在通过可视化分析工具来解决这些挑战，使得能让大模型更贴近人类需求，并为此提供改进方向。

本文的主要贡献：

1. 提出一个新的交互式分析自动化并排评估方法结果的工具

<div align="center"><img src="./Picture1.gif"></div>

<p align="center"><span style="font-size:12px">LLM Comparator用户界面</span></p>

## 相关工作

- Perform automatic side-by-side evaluation
- Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena
- In Proceedings of the 2023 Conference on Empirical Methods in Natural Language Processing (EMNLP)
- In International Conference on Machine Learning (ICML)

## 工具用途

LLM Comparator 是一个交互式和可视化的工具，用于并排评估大型语言模型的响应质量与安全性。它不仅提供定量评估，还提供评估结果的定性描述，并允许开发者通过点击特定的输入输出行来理解指标背后的含义。

这个工具主要是为大语言模型开发者设计的，他们可以借此检查调整过的模型是否比之前的版本更好。它也可以用来评估两个不同的模型，比较不同的提示策略。它能很好地帮助开发者获取他们需要的信息，以使大模型更加有用和安全。

### 工作原理

许多团队在开发大语言模型时都会使用一系列提示词数据集来评估当前版本的大模型能力。理想情况下，开发者希望生成大量的响应，并有统一的标准来测试它们的质量和安全性。 但是，在数据量较大时这会很困难。所以 LLM Comparator 利用了引入其他大语言模型作为裁判的想法[^1]，为用户提供了一个可以用于探索、验证、分析的工具。

为了更清晰地演示 LLM Comparator 的能力，作者提供了一个[Demo](https://pair-code.github.io/llm-comparator/)比较了 Gemma 7B 微调 1.0 和 1.1 两个版本的表现。 他们使用了 Chatbot Arena Conversations 数据集[^1]作为样本，这是一个公开可用的数据集，包含了在 LMSYS Chatbot Arena 上收集的 33,000 次对话。

[^1]: 参见相关工作第二篇：[Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena](#相关工作)

### 设计目标&任务

#### 目标一

促进汇总信息和单个示例之间的交互，方便用户识别感兴趣的部分并详细检查具体的提示和响应对。

#### 目标二

- **when**：在什么情况下模型 A 的表现优于模型 B？

- **why**：裁判大模型常用的理由是什么？为什么评估结果认为一个模型优于另一个？

- **how**：两个模型的响应有什么不同？可以观察到哪些定性模式？这些模式能否用于改进数据或模型？

#### 目标三

目标（DG3）： 提供对大量提示的评估结果的分析能力，帮助用户更有信心地辨别模型性能差异。

### 界面设计

#### 得分评估

在作者的[Demo](https://pair-code.github.io/llm-comparator/)中，大模型“裁判”（作者选用的是 Gemini Pro）认为 Gemma 1.1 全面优于 Gemma 1.0。

但是具体 Gemma 1.1 的提升如何进行量化评估呢？在 LLM Comparator 出现以前，要解答这个问题是非常麻烦和困难的。LLM Comparator 通过直观的可视化界面，使用户可以按任务分类(由数据集定义)来进行比较两个模型在同一领域的任务下的表现。

<div align="center"><img src="./Picture2.webp"></div>

<p align="center"><span style="font-size:12px">LLM Comparator的得分表明Gemma 1.1在61.6%的情况下表现优于Gemma 1.0</span></p>

#### 原因总结

为什么“裁判”会认为某个大模型更好？以 Demo 为例，Gemini Pro 分析后总结认为 Gemma 1.1 模型在回答上更加详细、准确，并且语言组织得更好。

考虑到效率和实现效果的平衡，作者在进行原因总结时没有选择对所有理由进行聚类，而是使用了基于大语言模型的新方法[^2][^3]。首先要求裁判大模型根据理由样本生成一组多样且具有代表性的标签，然后根据嵌入相似性将每个案例中的打分理由分配到相应的标签下。如果理由与标签之间的余弦相似度超过了一个阈值，则认为它们匹配。每个理由可以被分配到多个标签下。

[^2]: 参见相关工作第三篇：[In Proceedings of the 2023 Conference on Empirical Methods in Natural Language Processing (EMNLP)](#相关工作)
[^3]: 参见相关工作第四篇：[In International Conference on Machine Learning (ICML)](#相关工作)

<div align="center"><img src="./Picture5.webp"></div>

<p align="center"><span style="font-size:12px">由Gemini Pro提供的定性分析，帮助开发者对正在比较的两个模型之间的差异有了更细致的理解</span></p>

#### 案例交互

如果用户想要更进一步，可以通过点击具体的案例来检查“裁判”给出的评分和理由。作者为这部分的交互界面做了以下设计：

- 为了更直观看到两个模型回复的不同，相同的部分会用绿色高亮显示

- 如果在多轮评分中，大模型 A 胜出了，“裁判”会根据预设好的提示词从每轮生成的长文本理由中分点总结 A 更好的原因

- 平均得分直接展示在界面上，如果想要查看每轮得分情况可以直接点击查看

- 模型 A 和模型 B 用不同颜色做区分，从颜色可以直观判断哪个模型在评价中胜出

<div align="center"><img src="./Picture4.jpg"></div>

<p align="center"><span style="font-size:12px">案例交互板块设计</span></p>

#### 自定义函数

但是，在评估大模型表现的过程中，还是需要有人类地参与：开发人员可能会注意到 AI 裁判忽视掉的一些内容，尤其是当问题较为微妙或者涉及到安全问题时。因此，LLM Comparator 允许用户设置自定义函数，以便开发人员根据自己的需求检查大模型的回复是否具有某些特定形式。以[Demo](https://pair-code.github.io/llm-comparator/)为例，开发人员发现 Gemma 1.0 喜欢在回复中以“Sure！”开头，而这是完全没有必要的（开发人员据此改进了 Gemma 1.0 以避免出现这种情况）。

自定义函数能帮助发现一些重要的问题。大模型被标记为“clear”的回复有多易懂？大模型的回复是否用符号列表来组织语言？大模型的回答是否礼貌尊重？大模型的回答是否过度使用礼貌用语？大模型回答中常用哪些人称代词（可揭示文本的某些特点，比如性别倾向、正式程度等）？

<div align="center"><img src="./Picture6.webp"></div>

<p align="center"><span style="font-size:12px">右侧的自定义函数板块帮助用户识别Gemma 1.1和Gemma 1.0两个版本之间在语言模式上的不同</span></p>

## 观察研究

### 参与人员

本文采访观察了 6 位专家(p1-p2)使用 LLM Comparator 的界面和功能的过程，并根据反馈迭代产品设计。参与观察研究的 6 名专家均来自谷歌公司，他们中有软件工程师、研究员、数据科学家。所有参与的专家都有使用自动化并排评估的经验，一些人还使用过 LLM Comparator 的早期版本。

### 流程设计

对每位专家的采访和观察总用时 45 分钟，通过远程视频会议的方式进行。在专家签署同意书后，作者首先会进行了一个 10 分钟的访谈，重点了解参与者在模型评估任务中的工作角色。随后是 5 到 10 分钟的 LLM Comparator 使用教程。在之后的使用工具过程中，作者要求参与者在使用工具时提出及时反馈并记录。最后一个流程是一个简短的访谈，参与者完成全部流程后将获得 25 美元的补偿。

### 用户行为分析

#### 案例优先

P1-P2 喜欢优先查看 prompt 和对应的回复，他们认为这个过程很重要，因为自动评估可能会出错，所以要先确定大模型“裁判”是否和他们的判断相符。另外从具体示例中，他们能够检查自己对模型做的调整带来了哪些影响。

#### 经验思维

P3，P4 和 P5 善用他们之前的经验来观察大模型是否存在一些不理想的行为。P3 根据经验检查了那些含有“I'm sorry”和“Unfortunately”的回答，因为这些回答往往是模型拒绝或是无法完成给出的任务。P5 检查了回答里是否包含一些不必要的表达（比如：以“here is”开头）。根据他们的行为模型，作者设计了自定义的函数板块使他们便捷地实现上面的需求，并可以检查在原因总结中“裁判”是否关注到了这些内容。

#### 理由分析

理由聚类视图提供了一种全新的数据分析方式。P2 之前使用过该工具的早期版本，可以检查单个示例的评分理由。更新版本引入了理由聚类，提供了新的深入数据调查方法，以验证开发者对大模型行为的假设。此外，P3 在使用工具过程中注意到了一个理由聚类“Avoids harmful content”。通过应用该聚类的过滤器，他们高兴地看到模型在特定问题上的表现满足他们的期待。P6 从图表中注意到一个任务类别的胜率差异显著。通过应用该类别的过滤器，他能够自然地从一个关于简洁性的理由聚类中形成新的假设，找到新的改进方向。

## 总结

随着大语言模型的不断进步，超越单纯的就指标做比较和分析开始变得重要。在自动化的并排评估过程中，让人类也交互式地参与其中，能对大语言模型的表现进行必要的细粒度分析。LLM Comparator 在这方面非常有用。开发人员可以在作者团队最新更新的[Responsible Generative AI Toolkit](<[ai.google.dev](https://ai.google.dev/responsible/docs/evaluation?hl=zh-cn)>)中找到 LLM Comparator 和他们开发的其他工具。
