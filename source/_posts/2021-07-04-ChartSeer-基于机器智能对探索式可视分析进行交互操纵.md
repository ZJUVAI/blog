---
title: "ChartSeer-基于机器智能对探索式可视分析进行交互操纵"
tags: ["论文评述", "报告"]
date: 2021-07-01
author: 周杰辉
mathjax: true
---

论文：ChartSeer: Interactive Steering Exploratory Visual Analysis with Machine Intelligence

作者：Jian Zhao , Mingming Fan , Mi Feng

发表：IEEE Transactions on Visualization and Computer Graphics, 2020

在探索性可视分析（EVA）中，决定探索哪些数据变量和使用何种可视化形式是具有挑战性的，此外，缺少对分析方案的整体理解，也容易导致错误的分析路径。本文提出ChartSeer，利用GrammarVAE等深度学习技术来表征创建的图表并生成视觉摘要，同时根据用户交互推荐适当的图表，以促进用户进一步探索。

[组会报告PDF链接](https://www.jeffjianzhao.com/papers/chartseer.pdf)

[系统代码链接](https://github.com/jeffjianzhao/ChartSeer)

## 引言(Introduction)

探索式可视分析（Exploratory visual analysis, EVA）是一个开放式的迭代过程，分析师使用可视化方法，如绘制图表，来识别数据中有趣的问题和发现。通常应用于分析师有模糊的假设或不明确的任务。

然而，目前探索式分析还面临着以下挑战：
- 参数空间大（决定探索哪些数据变量以及使用哪些类型的图表）
- 缺乏整体理解（推进和引导探索式可视分析比较困难）
- 协作和异步场景

已经有一些自动生成图表推荐的工作，但通常需要某种程度的用户指导，例如用户输入查询规范或探索的属性，而且用户尚不清楚探索分析时的整体情况，以便他们能够明确（或隐含）地向系统提供适当的输入，不适当的输入会导致不必要的、杂乱的建议，导致分析师在探索式可视分析中走上无效的道路。

因此，本文提出了ChartSeer，通过图表的动态可视化和推荐，帮助分析师在探索性分析中获得指导。ChartSeer使用**元(meta)可视化方法**呈现已经创建的图表，以描述当前分析的状态，使分析师能够获得关于探索式可视分析的整体情况，识别用户行为的集群、趋势和差距，帮助用户找到分析空间的“漏洞”，以便在感兴趣的局部区域内自动生成适当的图表。为了实现这一点，ChartSeer通过**Grammar VAE**将图表映射为向量，并通过这种映射创建元可视化并实现交互式推荐。

为了评估ChartSeer，通过案例研究展示了该系统在异步协作环境下推进探索性可视分析的有效性，并进行了一项受控用户研究，比较ChartSeer与基线。结果表明，在使用ChartSeer时，参与者创建了更多的图表，涵盖了更多不同的数据变量和编码通道，具有更多样的可视角度，但更侧重于数据探索。参与者的使用模式和系统的挑战也进行了讨论。

## 相关工作(Related Work)

### 可视化相似性(Visualization Similarity)

可视化相似性可以为生成探索式可视分析的信息摘要提供重要的见解，如**分析覆盖面**和**可能的探索方向**。

度量相似性的方法主要有使用人工特征来建模可视化和使用数据驱动的方法来计算相似性。

ChartSeer采用深度学习技术将图表映射到语义向量，以测量图表相似性并生成图表。

### 元可视化(Meta-Visualization)

元可视化或元分析可视地呈现可视化的集合，被广泛应用于支持协作数据分析。

常见方法有记录已探索的数据维度，或利用标签、评论和可视化来突出探索式可视分析的整体状态等。
但用户仍然需要手动消费和探索综合信息，以确定未来探索和创建新图表的方向，当分析空间很大时，会增加用户的认知负担。

ChartSeer使用机器智能来简化这项任务，机器智能以交互方式向分析师推荐数据图表，以供进一步分析。

### 可视化推荐(Visualization Recommendation)

自动向分析师推荐合适的图表以促进探索式可视分析，尤其是当分析空间较大时。

常见的方法主要基于经验任务、感知模型、启发式或固定规则。还有一些数据驱动的方法，例如Data2Vis建立了一个端到端的神经网络，直接从Json数据生成Vega-Lite可视化，而VizML则从数据可视化的语料库中学习设计选择。

数据驱动的基于学习的方法允许更通用的解决方案，以避免诸如昂贵的规则创建和结果的组合爆炸等问题。因此，ChartSeer采用了这种方法，并采用深度学习将图表转换成用于摘要和推荐的语义向量。ChartSeer以交互方式推荐数据图表，与完全自动化的方法不同，ChartSeer 通过集成分析师的创造力和领域知识来支持推荐


## ChartSeer设计(The Design of ChartSeer)

### 设计考量(Design Considerations)

**总体目标：** 通过将人和自动化相结合来有效引导 EVA

- D1: 提供图表的语义概述，以鼓励整体理解
- D2: 利用认知和算法指导，提出未来探索的途径
- D3: 允许交互式操作来驱动推荐
- D4: 支持图表的可视调查、操作和创建


### 系统概览(System Overview)

后端包含数据存储和分析、图表编码解码模块，前端包括检验、摘要和推荐面板。如图所示：

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-System.png)


## 图表特征化(Chart Characterization)

用适当的模型或表示来特征化图表构成了图表概要和推荐的基础(D1, D2)，使用编码器解码器方法可以将图表转换成语义向量（即嵌入），解码器还可以用于生成推荐图表(D2, D3)。

### 语法制导的变分编码器(Grammar Variational Autoencoder, GVAE)

**普通VAE的问题：**
- 可视化语法通常是非常严格的
- 微小的改动就会导致无效

**一些Observation：**
- 正式语言符合上下文无关文法
- 可视化语法是已知和固定的
- 语法解析是唯一的，不会有歧义
- 下推自动机可以识别上下文无关文法

VAE可以学习到问题空间中的数据点与连续潜在空间中的变量之间的双向映射，基于上下文无关文法(CFG)的GVAE可以使解码器生成有效的数据样本，如下图所示：

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-GVAE.png)

由于Vega-Lite的图表规范是分层的，每个图表都可以被视为由CFG中的一组规则生成的解析树。下图显示了一个图表、它的Vega-Lite以及生成JSON树的规则。

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-CGF.png)

因为目标是使用GVAE来获得独立于特定数据集的图表编码器和解码器，所以Vega-Lite中的数据字段被替换为公共标记，即，用NUM替换数量字段，用STR替换分类/序数字段。此外，与可视编码无关的规范，如data和schema，也被排除在外。

### 模型训练和评估(Model Training and Evaluation)

最终数据集包含1-3个数据变量的9925个Vega-Lite的实例，80%用于训练，10%用于验证，10%用于测试。本文还将GVAE与LSTM自动编码器进行了对比，实验结果显示GVAE表现更好，虽然并不完美，但提供了一个相对较好的开始。

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-GVAE-LSTM.png)


## ChartSeer系统(The ChartSeer System)

用户界面如图所示，由(a)图表检查面板，图表汇总面板(包括(b)汇总视图、(c)数据表视图和(d)图表列表视图)和(e)图表推荐面板组成。(f)用户正悬停在图表上。

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-User-Interface.png)


### 图表概要(Chart Summarization)

基于图表编码器生成的嵌入特征，降维用于将图表从高维特征空间投影到二维空间，然后将它们可视化为圆形图符(D1)。

使用MDS降维，相似性考虑了数据变量与可视编码 (Euclidean + Jaccard)，如下式所示，用户可以通过滑块对权重进行调整。

$$
D_{w}(m, n)=\alpha\left|v_{m}-v_{n}\right|+(1-\alpha)\left(1-\frac{f_{m} \cap f_{n}}{f_{m} \cup f_{n}}\right)
$$

当添加或删除图表时，利用Procrustes transformation找到两组位置之间的最大重叠，用于最小化布局变化。下图显示了图表投影，(a)α=0，只考虑数据变量的距离，(b)α=0.5，数据变量与可视编码的加权距离，(c)α=1，只考虑可视编码的距离

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-Glyph.png)

### 可视表示(Visual Representation)

**Glyph设计：** 圈内显示图表类型，外环每个彩色段表示一个数据变量，用户ID标志附在旁边。

设计时还考虑了Alternative design（彩色点、饼图），但本文采纳的Glyph可以快速浏览图表的必要信息，无需深入图表查看。利用层次聚类的方法对glyph进行聚合，并利用灰色背景表示，文本大小表示变量的频率，颜色与数据表中的相同。如下图所示。

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-Cluster-Glyph.png)

### 图表推荐(Chart Recommendation)

从图表摘要面板中，分析师可以获得到目前为止探索式可视分析的概述，并通过观察视图中的模式来决定未来的探索(D2)。为了促进这一任务，ChartSeer还支持使用交互式指导(D3)的图表推荐。

推荐过程一共分为四步，具体如图所示
1. Sampling：基于用户点击的二维位置进行采样
2. Reverse projection：反映射回高维空间(本质解一个优化问题)
3. Decoding：高维向量输入解码器，映射回Vega-Lite的图表
4. kNN searching and filtering in variables：替换数据和字符串字段，并移除无效的Vega-Lite规范

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-Recommendation.png)


实现细节：
- #sample: 3m（m default 5，用户指定）
- Sample radius r = 4% * view size
- K = 5
- Threshold distance = 0.3 * max distance between existing charts
- 优化：拟牛顿法
- 推荐：CompassQL (Voyager)

### 图表检查(Chart Inspection)

图表检查器允许分析师编辑他们创建的由系统推荐的图表(D4)。
- 专家可以直接编辑 Vega-Lite，新手可以使用下拉控件
- 可以选择更新或添加图表，Summarization View 会同步更新

## 案例研究(Case Study)

为了了解ChartSeer如何在分析师的工作流程中使用，与一位在大型公司有五年工作经验的数据科学家进行了一个小时的访谈。作为日常工作的一部分，他使用ggplot2、Matplotlib和Vega-Lite来执行探索式可视分析，如图所示。

U1和U2的结果互相交错，U3则侧重于其他方面。通过悬停查看发现：
- U1：机构财务
- U2：学生财务
- U3：学术

空白区域推荐图表反映了财务与学术，ChartSeer也推荐了未设想到的图表，鼓励用户探索数据。

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-Case-Study.png)

## 受控用户研究(Controlled User Study)

本文还进行了一项对照研究，以评估ChartSeer的新功能，并将其与类似的基线(具有浏览数据集和创建图表的功能)进行比较，如图所示。

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-Baseline.png)

**研究问题：**
- 系统对参与者的分析结果有什么影响
- 参与者对新功能的态度是什么
- 参与者如何使用这些功能？

**分析指标**
- 覆盖率
- 独特性
- TLX 量表

参与者结果分析(左)和问卷评分(右)如图所示。对于每项测量，报告95%置信区间的组均值，以及Mann-Whitney检验的结果(*和**分别表示p < .05和0.01)。

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2021-07-04-ChartSeer-基于机器智能对探索式可视分析进行交互操纵/ChartSeer-Evaluation.png)

**结论：**
- 使用更广泛的数据变量和可视编码
- 鼓励对数据变量进行更集中的探索
- 导致对于数据更丰富多元的可视化
- 鼓励用户添加更多的图表

## 讨论

- Summarization视图中的维度并没有实际的含义，且未包含时间信息
  - AxiSketcher, forward and backward projection
- 分析师有时希望生成满足特定约束的图表
  - 设计基于语义的用户交互
- 分析界面的子空间中有用的视图可能有限
  - 设计视觉线索帮助分析师跳出“条条框框”的限制
- 模型仍有改进空间
  - 收集更多的数据，结合规则/启发式方法
- 冷启动
  - 随机样本产生初始图表
- 受控用户研究
  - 后来的用户可能没有太多的空白区域来探索，总结和推荐分别评估

## 结论：

一句话：本文提出了 ChartSeer，通过结合交互式可视化与机器学习，来引导探索式可视分析。

**主要贡献：**
- 从文献中提取设计考量
- 利用深度学习模型实现元可视化和图表的交互式推荐，支持多会话和异步的探索式可视分析
- 案例研究与受控用户研究的发现

## 思考

**Critical thinking**
- 图表嵌入重建精度不足
- 图表还比较简单

**Creative thinking**
- 深度学习的压缩表达缺乏语义信息，可以结合交互选择与统计信息
- 交互反馈信息更新

**How to apply to our work**
- 语法中的交互信息
- Graph
- VR/AR/MR中的协作


[^1]: Zhao, Jian, Mingming Fan, and Mi Feng. Chartseer: Interactive steering exploratory visual analysis with machine intelligence. IEEE Transactions on Visualization and Computer Graphics (2020).
[^2]: Kusner, Matt J., Brooks Paige, and José Miguel Hernández-Lobato. Grammar variational autoencoder. International Conference on Machine Learning. PMLR, 2017.