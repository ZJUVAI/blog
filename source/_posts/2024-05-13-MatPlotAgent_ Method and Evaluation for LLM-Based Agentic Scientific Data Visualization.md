---
title: "MatPlotAgent: Method and Evaluation for LLM-Based Agentic Scientific Data Visualization"
tags: ["论文评述", "报告"]
date: 2024-15-13
author: 杨熠
mail: yang-yi@zju.edu.cn
mathjax: true
---


# 05.13 MatPlotAgent: Method and Evaluation for LLM-Based Agentic Scientific Data Visualization

科学数据可视化在研究中起着至关重要的作用，它能够直接显示复杂信息并帮助研究人员识别隐性模式。尽管大型语言模型 （LLM） 很重要，但使用大型语言模型 （LLM） 进行科学数据可视化仍未得到探索。此篇论文介绍了MatPlotAgent，这是一个高效的模型无关LLM代理框架，旨在自动执行科学数据可视化任务。MatPlotAgent利用代码LLM和多模态LLM的能力，由query理解、迭代调试的代码生成和纠错的可视化反馈机制三个核心模块组成。为了解决该领域缺乏基准的问题，本篇论文还提出了 MatPlotBench，这是一个由 100 个人工验证的测试用例组成的高质量基准测试。此外，本文还介绍了一种利用 GPT-4V 进行自动评估的评分方法。实验结果表明，MatPlotAgent 可以提高各种 LLM 的性能，包括商业模型和开源模型。此外，所提出的评估方法与人工注释的分数具有很强的相关性。

# 研究背景 
•数据可视化：原始数据 ➡️ 信息丰富且易于理解的数据
•存在的问题：
•Time-consuming
•Labor-intensive
•本文提出的MatPlotAgent利用了LLMs和multi-modal LLMs在保证精度的同时提高数据可视化的效率。
•MatPlotAgent包含三个模块：
•query understanding：理解用户提供的需求
•code generation module ：用代码对原始数据进行精确预处理并生成图表
•visual feedback module：发现绘制草稿中的错误，并向代码生成模块提供视觉反馈以纠正错误
# 相关工作

- Code LLMs
   - SantaCoder
   - StarCoder
   - Code Llama
   - DeepSeekCoder
   - Magicoder
- LLM Agents
   - OpenAgents  
   - Voyager
   - ChatDev
# 本文工作

- 本文贡献：
   - MatPlotBench：实现对科学数据可视化的AI方法的自动定量评估。
   - MatPlotAgent：基于本文提出的可视化反馈机制提高各种LLM的性能

![image.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716190242695-f907b67b-3fea-46b1-a804-025b2d3bebc3.png#align=left&display=inline&height=287&margin=%5Bobject%20Object%5D&name=image.png&originHeight=574&originWidth=1160&size=473741&status=done&style=none&width=580)
# 科学数据可视化任务

- 输入：
   - User query x：可视化要求，包括可视化类型、要绘制的数据、单个元素或整个绘图的结构或空间要求以及美学偏好
   - DataD：数据点 {d，…，d}的集合。
- AI 系统 f 应输出一个可以满足用户需求的图 V：

V = f(x, D)
# MatPlotBench
**Data Collection**

- Principles：
   - （1）涵盖多种类型：包括广泛的图表类型，不仅包括最常用的，还包括稀有但有用的;
   - （2）包含代表性实例：确保测试实例反映科学数据可视化的代表性特征，如数据复杂度的多样性;
   - （3）平衡简单和具有挑战性的问题：在基准测试中包括不同难度的问题。
- Selecting Original Examples ：
   - Matplotlib Gallery（75 examples）：bars, lines, markers, pie charts, polar plots, contour plots, statistics plots, 3D plots, text annotations, radar charts, shapes, scales, axes, spines, subplots…
   - OriginLab GraphGallery（25 examples）：Sankey diagrams, sunburst charts, radial plots, chord diagrams, streamplots…
- Preliminary Query Generation ：
   - Matplotlib Gallery ：使用 GPT-4 将每个原始示例中的代码转换为preliminary queries
   - OriginLab GraphGallery（只有图像）：使用 GPT-4V 将每个图像转换为preliminary queries
- Data Replacement：
   - 对 Matplotlib 库中的示例进行数据替换，将原始数据点替换为新生成的数据点，同时保持其他因素（如绘图类型）不变。
- Human Modification：
   - 注释者的任务是纠正错误、消除歧义并添加任何遗漏的基本信息。
- Updating Ground-Truth Figures：
   - 手动编写代码来绘制 Matplotlib 示例的ground truth
- Human Verification：
   - 检查用户查询和ground truth是否一致
   - 纠正描述不当的要素和缺少的说明
   - 删除冗余和不正确的描述
   - 产生最终的benchmark：100 个高质量（query、原始数据、ground truth数据）三元组

**Automatic Quantitative Evaluation  **

- Correlation with Human Evaluation ：
   - 从总benchmark中迭代采样一个由 𝑛 个示例组成的子集
   - 使用 GPT-3.5 和 GPT-4 在 MatPlotBench 上生成图表
   - 对生成的图表进行自动和人工评估
   - 这个过程重复 k 次，得到每种评估类型的 k 个数据点：
      - 自动评分：𝐴 = {a,  · · ·,  𝑎}  
      - 人工评分：𝐻 = {ℎ,  · · ·,  ℎ}  
- 利用 𝑠𝑐𝑖𝑝𝑦 提供的统计函数来计算 Pearson 相关系数 𝑟 和 p-value 𝑝：
   - GPT-4：𝑟=0.876 和 𝑝=7.41𝑒−33
   - GPT-3.5： 𝑟=0.836 和 𝑝=2.67𝑒−27

![image1.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716190768094-d641252e-c6a9-4fdf-89f4-88874087d23f.png#align=left&display=inline&height=215&margin=%5Bobject%20Object%5D&name=image.png&originHeight=430&originWidth=588&size=71986&status=done&style=none&width=294)
# MatPlotAgent

- MatPlotAgent由三个模块组成：
   - Query Expansion Module
   - Code Agent
   - Visual Agent

![image2.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716190828003-2acc3421-10b0-4f37-913e-e691c5e7bf07.png#align=left&display=inline&height=341&margin=%5Bobject%20Object%5D&name=image.png&originHeight=682&originWidth=1224&size=415312&status=done&style=none&width=612)

- Query Expansion Module
   - User Query ➡️ 一系列清楚、详细的指令
   - 基于LLM制定了一个生成图表的完整计划
      - 调用什么库函数
      - 如何正确设置每个函数中的参数
      - 如何准备数据
      - 如何操作数据

![image3.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716190893348-133ab930-568e-47a4-8290-b0275acf1630.png#align=left&display=inline&height=176&margin=%5Bobject%20Object%5D&name=image.png&originHeight=352&originWidth=1864&size=306880&status=done&style=none&width=932)

- Code Agent
   - 详细指令 ➡️ 绘制图表的代码
   - Code Generation：使用适当的库和函数生成代码
   - Self-Debugging：
      - Code LLM 迭代识别和纠正代码中的错误
      - multi-modal LLMs提供可以更好满足用户需求的图表改进建议

![image4.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716190958432-48e392d0-f30d-4ff6-a07e-56a8c71ca700.png#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&name=image.png&originHeight=442&originWidth=1190&size=255257&status=done&style=none&width=595)

- Visual Agent
   - 采用了multi-modal LLMs
   - Principles
      - Match Type and Data：保证图表与提供的数据一致
      - Customize：增强颜色或标签以提高图表的信息量
      - Adjust and Improve:生成一些优化图表的建议反馈到Code Agent
# 实验

- Setup
- Models
   - code LLMs：GPT-4, GPT-3.5, Magicoder-SDS-6.7B, Deepseek-coder-6.7Binstruct, Deepseek-coder-33Binstruct, WizardCoder-Python33B-V1.1 和 CodeLlama-34BInstruct
   - Visual Agent：GPT-4V 和 Gemini Pro Vision
- Evaluation
   - MatPlotBench 用 GPT-4V 进行 Automatic Evaluation
   - code LLM Evaluetion：
      - Direct decoding：给定query，模型直接生成绘图代码
      - Zero-Shot Chain-of-thought：模型使用零样本 CoT 机制进行推理
      - MatPlotAgent：用MatPlotAgent 框架驱动query expansion module 和 code agent
- Main Results
   - 不同 LLM 在 MatPlotBench 上的性能
      - Direct decoding
      - Zero-Shot CoT
      - MatPlotAgent
   - GPT-4 和 GPT-3.5使用 Gemini Pro Vision 作为 visual agent 的结果

![image5.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716191116690-add063a3-a6fe-400c-b428-9f3524dd7a59.png#align=left&display=inline&height=237&margin=%5Bobject%20Object%5D&name=image.png&originHeight=474&originWidth=1222&size=146858&status=done&style=none&width=611)
![image6.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716191122580-3668091d-18f1-4945-b530-79d3282f021e.png#align=left&display=inline&height=159&margin=%5Bobject%20Object%5D&name=image.png&originHeight=318&originWidth=688&size=68101&status=done&style=none&width=344)

- Results on Qwen-Agent Code Interpreter Benchmark
   - GPT-4  
   - GPT-4 + MatPlotAgent
   - GPT-4 + MatPlotAgent - Visual Feedback

![image7.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716191165067-eead6b04-10b6-461b-838f-6c1841cd95fc.png#align=left&display=inline&height=151&margin=%5Bobject%20Object%5D&name=image.png&originHeight=302&originWidth=1178&size=86270&status=done&style=none&width=589)

- Ablation Study
   - 视觉反馈机制的影响
      - 定性分析（图 4）
      - 定量分析（表 4）

![image8.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716191210574-e6564d21-aa14-49a5-b863-b804003f269d.png#align=left&display=inline&height=440&margin=%5Bobject%20Object%5D&name=image.png&originHeight=880&originWidth=1220&size=758714&status=done&style=none&width=610)
![image9.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716191218594-7604509f-6150-4749-bd01-d28b437b0a7a.png#align=left&display=inline&height=157&margin=%5Bobject%20Object%5D&name=image.png&originHeight=314&originWidth=646&size=57756&status=done&style=none&width=323)

- Case Study
   - 展示了 GPT-4，GPT-3.5 和 Magicoder-S-DS-6.7B在三个难度递增case上的效果

![image10.png](https://yuque.zju.edu.cn/images/yuque/0/2024/png/30961/1716191282021-7dd137e5-9b52-4d6d-970a-193c4cb4e27b.png#align=left&display=inline&height=419&margin=%5Bobject%20Object%5D&name=image.png&originHeight=838&originWidth=1202&size=451513&status=done&style=none&width=601)
# 总结

- 本文提出评估和增强LLM在科学数据可视化方面的能力：
   - MatPlotBench是一个严格的基准测试，支持与人工评估高度一致的自动定量评估
   - MatPlotAgent是一种与模型无关的机制，采用视觉反馈来增强LLM的绘图能力
# Limitations

- 科学数据可视化的需求在不同学科之间可能会有很大差异：
   - MatPlotBench是为一般科学数据可视化而开发的
   - MatPlotBench无法涵盖所有特定领域的要求，从而可能限制其对某些领域的适用性
