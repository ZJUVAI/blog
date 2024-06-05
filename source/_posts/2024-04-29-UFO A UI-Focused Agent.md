---
title: "UFO : A UI-Focused Agent for Windows OS Interaction"
tags: ["论文评述", "报告"]
date: 2024-04-29
author: 沈健
mathjax: true
---

# UFO: A UI-Focused Agent for Windows OS Interaction

论文：UFO: A UI-Focused Agent for Windows OS Interaction

作者：Chaoyun Zhang, Liqun Li, Shilin He, Xu Zhang, Bo Qiao, Si Qin, Minghua Ma, Yu Kang, Qingwei Lin, Saravan Rajmohan, Dongmei Zhang & Qi Zhang

发表：arXiv 2024.05

本文提出了 UFO，一种创新的以用户界面为中心（UI-Focused）的智能体，旨在满足 Windows 操作系统上应用程序的用户请求。UFO 采用双代理框架，精细地观察和分析 Windows 应用程序的图形用户界面和控制信息。这使得智能体能够无缝地在单个应用程序内部及其之间导航和操作，以满足用户请求。该框架还包含一个控制交互模块，使得智能体的行为无需人工干预就可以落地，并实现了完全自动化执行。因此，UFO 将繁琐且耗时的过程转变为仅通过自然语言命令就可实现的简单任务。我们对 UFO 进行了包含 9 个主流 Windows 应用程序的测试，涵盖了反映用户日常使用情况的各种场景。从定量指标和真实案例研究得出的结果强调了 UFO 在满足用户请求方面的卓越有效性。据我们所知，UFO 是第一个专门为 Windows OS 环境完成任务而设计的 UI 智能体。

# 研究背景

## 智能体

近来，大型语言模型的应用不断涌现并蓬勃发展。其中，智能体的出现显著扩展了 LLM 的能力。智能体通过观察、规划、记忆和反应等能力进行行动。例如，AutoAgents能够根据不同的任务，自适应地生成和协调多个智能体，构建一个多智能体协作团队。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908613.png)

## 基于 LLM 的 GUI 智能

一个值得注意的应用方向是基于视觉大型语言模型（VLM）构建智能体，并与软件的用户界面（UI）或图形化用户界面（GUI）互动。用户可以用自然语言表达请求，智能体能够将其落实到物理设备中。这促使了大语言模型转变为大行动模型（Large Action Models）。使智能体的决策能够体现在物理行动中，并产生切实的现实世界影响。已有多篇工作将基于LLM 的智能体应用在手机端UI上自动完成用户命令。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908401.png)

但是，专为 Windows 系统定制智能体，以实现用户请求的这个方向仍然未被探索。由于 Windows 在计算机操作系统市场中占据较高份额，而且有海量应用程序和图形用户界面建立在该系统的基础上。此外，Windows 中存在许多多步骤和跨应用的操作，大大降低了用户体验和效率。

# 主要工作

### Overview

因此，本文提出了UFO，专注于Windows UI的智能体，上图展示了UFO的整体架构。UFO包括：（1）一个应用选择智能体（AppAgent）。（2）一个动作选择智能体（ActAgent）。这两个智能体利用 GPT-Vision 的多模态能力来理解应用程序界面并满足用户的请求。（3）系统还包含一个控制交互模块，它将智能体生成的动作落实到应用程序中做执行。

系统的流程为：在接收到用户请求后，AppAgent开始对需求进行分析。它从当前活动的应用程序中选择一个合适的应用程序，并制定一个全局计划来完成请求。这个计划然后传递给ActAgent。一旦选定了合适的应用程序，应用程序会被聚焦在桌面上。然后系统会捕获当前应用程序用户界面的截图，并注释所有可用控件。ActAgent的任务是选择界面中的一个控件进行操作，随后通过控制交互模块执行在所选控件上的具体动作。这一过程递归地持续进行，直到用户请求在选定的应用程序内成功完成。如果用户请求跨越多个应用程序，ActAgent完成当前应用程序上的任务后，会要求AppAgent切换到后续的应用程序。这一迭代过程持续进行，直到用户请求的所有方面都完全完成。下面，将探讨UFO框架内每个组件的具体细节。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908402.png)

### AppAgent

AppAgent负责选择一个活跃的应用程序来满足用户请求，或在必要时切换到新的应用程序。其架构如上图所展示。AppAgent接受以下信息作为输入：

- User Request：提交给UFO的原始用户查询。
- Desktop Screenshots：当前桌面的截图。
- Application Information：可用的活跃应用程序详情的列表，包括它们的名称和类型。桌面截图和应用程序信息使AppAgent能够理解当前状态，并限制其应用程序选择的范围。
- Memory：包括之前的思考、评论、动作和执行结果。记忆作为过去请求完成情况的历史记录，帮助AppAgent基于先前的经验做出明智的决策。
- Examples：选择应用程序的文本示例，作为任务的提示，起到少样本学习的效果。

在输入所有相关信息后，AppAgent使用GPT-V生成以下输出：

- Observation：当前桌面窗口截图的详细描述。
- Thoughts：根据Chainof-thought (CoT) 范式，完成给定任务的推理逻辑。这两项鼓励AppAgent仔细分析当前状态，为其逻辑和决策过程提供详细的解释。
- Selected Application：所选应用程序的标签和名称。（结果）
- Status：任务状态，标记为“CONTINUE”或“FINISH”。
- Global Plan：完成用户请求的后续行动计划，通常是一个全局的、粗粒度的计划。
- Comment：提供的额外评论或信息，包括简短的进度总结和需要强调的点。可以给用户发送评论，报告进度，强调潜在问题或解答任何用户查询。

AppAgent识别出选定的应用程序，UFO将在此应用程序内采取特定动作以满足用户请求，由ActAgent负责执行这些动作。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908403.png)

### ActAgent

ActAgent作为AppAgent之后的下游实体，负责在选定的应用程序上执行特定动作以满足用户请求。其输入和输出如图所示。ActAgent接受以下输入：

- User Request：提交给UFO的原始用户查询，与AppAgent相同。
- Screenshots：截图包括三个部分：（i）Previous Screenshot；使用红色矩形突出显示上一步选定的控件，用于理解上一步操作的执行和分析动作的影响。（ii）Clean screenshot；帮助Agent理解应用程序的当前状态，（iii）Annotated screenshot。用数字标注每个控件，便于更好地理解UI元素的功能和位置，不同类型的控件用不同的颜色标注以进行区分。
- Control Information：在选定应用程序中可用的操作控件的名称和类型。
- Memory：之前的思考、评论、动作和执行结果，与AppAgent相同。但是它不仅是作为Agent的提醒，而且它提供了跨应用程序通信的关键渠道。之前的执行结果，比如从文档中提取的文本或图像的描述，存储在记忆模块中。ActAgent可以选择性地将这些信息纳入当前的动作中，例如用来自不同来源的文本编写电子邮件。
- Examples：用于动作选择的文本示例，作为提示。

ActAgent仔细分析所有输入信息后，输出以下内容：

- Observation：当前应用程序窗口截图的详细描述，可以用于分析上一个动作是否生效。
- Thoughts：当前动作决策背后的逻辑推理过程。
- Selected Control：操作所选控件的标签和名称。
- Function：应用于控件的特定函数及其参数。用于提供给控制交互模块。
- Status：任务状态，标示为“CONTINUE”（如果需要进一步动作）、“FINISH”（如果任务完成）、“PENDING”（如果当前动作需要用户确认）、“SCREENSHOT”（如果智能体认为当前标注太多难以理解，需要进一步的截图以注释更少的控件集）、以及“APP SELECTION”（当前应用程序上的任务完成，且需要切换到不同的应用程序）。
- Local Plan：更精确和细致的未来动作计划，以完全满足用户请求。
- Comment：额外的评论或信息，包括简短的进度总结、突出的点或计划的变化，类似于AppAgent所提供的。

如果任务未完成，ActAgent会将功能应用于所选控件，然后观察执行动作后的应用程序状态。迭代重复上述过程，直到用户请求完全完成或需要切换到不同的应用程序。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908404.png)

### Control Interaction

UFO 利用 pywinauto 来检查应用程序的所有可操作控件，检索它们的精确位置并用边框来添加注释。pywinauto 为每个控件提供了丰富的上下文，包括其名称、类型和标题，这些都是控件和操作选择的关键信息。

UFO 主要关注以下10种具有高度相关性的控件类型。其中包括 Button按钮、Edit编辑、TabItem选项卡项、Document文档、ListItem列表项、MenuItem菜单项、TreeItem树项、ComboBox组合框、Hyperlink超链接、ScrollBar滚动条。这些可以覆盖应用程序中的大多数控件。

可以应用于控件的功能操作包括：

- Click：用鼠标点击控制项，可选择左键或右键点击、单次或双击。
- Scroll：垂直或水平滚动控制项，使隐藏内容可见。
- SetText：将文本输入到可编辑控件中，模仿键盘行为。
- GetText：检索控件的文本信息。
- Annotate：捕获当前应用程序窗口的屏幕截图，并在 GUI 上注释控制项。
- Summary：基于干净的屏幕截图总结当前应用程序窗口的观察结果。

前4项是 pywinauto 原生支持的常见功能，涵盖了大多数日常的图形用户界面操作。后2项是为了满足 UFO 特殊需求而定制的操作。Annotate 允许对 GUI 进行重新注释，来获得更简洁的控件列表，Summary 使 UFO 能够用文本描述其视觉观察结果，以满足用户请求。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908405.png)

### Special Design Consideration

UFO 还融入了一系列专门针对 Windows 系统定制的设计元素。

### Interactive Mode

UFO 为用户提供了交互的能力，而不是单步完成任务。任务完成后，用户可以提出一个全新的任务让 UFO 执行，或在 UFO 可能缺乏熟练度的任务中协助操作。

### Action Customization

UFO 目前支持的动作并不详尽，但是系统可以进行大幅扩展和定制。它允许加入键盘快捷键、宏命令、插件等功能。一个例子是 Summary 动作，它利用 UFO 的多模态能力按需观察和描述屏幕截图。

为了实现这种级别的定制，用户可以注册他们的定制操作。包括指定目的、参数、返回值，以及提供示例。然后，这些信息会被整合到 UFO 的 prompt 中以供参考。注册过程完成后，定制操作就可以被 UFO 自动调用执行。

### Control Filtering

有些情况下，系统可以检测到数百个控件项。然而，标注所有这些控件可能会使得应用截图变得杂乱，从而影响系统性能。因此，实现过滤机制变得至关重要。

为了应对这一挑战，UFO采用了两种控件过滤方法，包括 hard level 和 soft level。在 hard level 上，基于用户指定的特定控件类型限制候选控件。在 soft level 上，赋予 UFO 动态决定是否重新生成一个更简洁的控件列表的能力。当 UFO 感知到控件数量过多时，该能力会被触发。

### Plan Reflection

虽然两个智能体都负责发起计划以满足用户请求，但应用程序 UI 的实际状态可能并不总是与预期条件一致。因此，计划和行动都应相应地进行调整。

为了应对这种动态性，UFO 的 prompt 中要求其在每一个决策步骤中持续修正其计划。作者指出，该设计显著提高了 UFO 系统的性能。

### Safeguard

UFO 内置了一种安全防护机制，在执行某些安全性操作前会寻求用户确认。例如：发送邮件、删除或修改文件、访问摄像头和安装应用程序等。

# 评估

## Benchmark & Baselines

为了全面评估在各种 Windows 应用程序中的性能，本文开发了一个名为 WindowsBench 的基准测试。这个 benchmark 包括50个用户请求，涵盖了9个日常任务中常用的热门 Windows 应用程序。对于每个应用程序，本文设计5个不同的请求，另外5个请求涉及跨多个应用程序的交互。以下列出了部分 WindowsBench 中的示例。

鉴于现有基于 Windows 系统的智能体的缺失，本文选择了 GPT-3.5 和 GPT-4 作为 baseline 模型。由于这些模型缺乏直接与应用程序交互的能力，作者 prompt 它们提供 step-by-step 的指令以完成用户请求。然后，人类作为它们的代理人来执行操作。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908407.png)

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908408.png)

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908409.png)

## 定量结果

在评估指标方面，作者从四个角度评估系统性能：成功率、步骤数、完成率和安全率。成功率判断智能体是否成功完成了请求。步骤指的是智能体完成任务所需采取的动作数量。完成率是正确步骤数与总步骤数的比率。最后，安全率衡量 UFO 在请求涉及敏感操作时请求用户确认的频率。评估结果如下表所示。

UFO 在 benchmark 中取得了86%的成功率，比表现最好的 GPT-4 高出一倍多。此外，UFO 展现出最高的完成率和最少的步骤完成任务，表明其能够采取更精确的行动。从安全角度来看，UFO 达到了85.7%的最高的 safeguard rate，这证明它可以准确地分类敏感请求。

baseline 的较差表现可归因于两个主要因素。首先，两个 baseline 都缺乏与真实应用环境直接交互的能力，依赖人类作为代理来执行动作。这一限制导致了无法适应环境变化和反馈。其次，baseline 仅接受文本输入，忽略了 GUI 交互中视觉能力的重要性。这一弱点阻碍了它们在 Windows 上完成用户请求的效果，其中视觉信息有时至关重要。

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908410.png)

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908411.png)

## 定性结果

![Untitled](https://gcore.jsdelivr.net/gh/silent-shen/Online_Image/202406051908412.png)

# 结论

## 讨论

当前 UFO 框架仍存在的一些限制。首先，目前可用的 UI 控件和操作受限于 pywinauto 和 Windows UI Automation 所支持的范围。为了增强 UFO 的能力，作者计划通过集成专用的 GUI 模型进行视觉检测。

其次，作者发现 UFO 在探索小众的或不常见的应用程序UI时面临挑战。在这种情况下，UFO 可能需要大量时间来导航和识别正确的操作。为了解决这个问题，作者提议利用在线搜索引擎作为 UFO 的外部知识库。

## 结语

总的来说，本文基于 GPT-Vision 探索了智能体与 Windows 系统界面的交互任务。为从事日常计算机活动的用户简化了各种任务，将冗长和乏味的过程转换为仅通过自然语言命令即可完成的简单任务。这使 UFO 定位为 Windows OS 的一款用户友好且自动化的辅助工具，有效降低了使用的总体复杂性。

# 参考

 [1] [https://arxiv.org/abs/2402.07939](https://arxiv.org/abs/2402.07939)

 [2] [https://zhuanlan.zhihu.com/p/685614612](https://zhuanlan.zhihu.com/p/685614612)

 [3] [https://www.bilibili.com/video/BV1VA4m1G7Jd/](https://www.bilibili.com/video/BV1VA4m1G7Jd/)