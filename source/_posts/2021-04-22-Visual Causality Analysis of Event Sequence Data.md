---
title: "Visual Causality Analysis of Event Sequence Data"
tags: ["论文评述", "报告"]
date: 2021-04-22
author: 王智勇
mail: zerowangzy@zju.edu.cn
mathjax: true
---

### 作者
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618901326843-c434512a-458e-46a9-9f61-6403f2c64fee.png)
本文第一作者是同济大学曹楠老师组（[同济大学智能大数据可视化实验室](https://idvxlab.com/index.html)）的博士生金卓宸，通讯作者曹楠老师，二作是他们实验室的华师大访问博士生郭姝男，三作他们的硕士生。Daniel Weiskopf和David Gotz都是这篇文章的合作老师。
### 背景
#### 事件序列（event sequences）和因果关系（Causality）
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618902462772-6330558c-685c-47d9-812b-42e5a61c82a3.png#align=left&display=inline&height=295&margin=%5Bobject%20Object%5D&name=image.png&originHeight=590&originWidth=1131&size=220166&status=done&style=none&width=565.5)
事件序列以一系列带时间戳事件。真实世界中处处充满了事件序列，例如，张三中午十二点在吃饭，下午一点开会，晚上10点打王者荣耀。分析时间事件序列可以帮助分析人员提取事件之间的因果关系。比如临床事件之间的因果关系可以指导医生学习病因，并找到更有效的治疗方法。
在时间数据中，因果关系的发现通常基于格兰杰因果关系理论（Granger Causality）。Granger因果关系能够根据增量可预测性来描述时间数据中的因果关系：
如果事件A的出现增强了事件B的可预测性，则A Granger引起B。具体可以参考：[格兰杰因果关系检验](https://www.bilibili.com/video/BV1P54y1C7nt?from=search&seid=10683392941931191364)
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618902952537-8d1b8276-c041-4c4b-92e9-5b4edb8507a0.png#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&name=image.png&originHeight=442&originWidth=798&size=100932&status=done&style=none&width=399)
本文的目的，就是从事件序列数据中，分析出格兰杰因果关系
#### 挑战
过去，学者通过

1. Graphical Medeling
> A. C. Lozano, N. Abe, Y. Liu, and S. Rosset. Grouped graphical granger modeling methods for temporal causal modeling. In ACM SIGKDD, pp. 577–586, 2009.

2. Hawkes Processes
> H. Xu, M. Farajtabar, and H. Zha. Learning granger causality for hawkes processes. In ICML, pp. 1717–1726, 2016

3. Deep Neural Networks
> M. Nauta, D. Bucur, and C. Seifert. Causal discovery with attentionbased convolutional neural networks. Machine Learning and Knowledge Extraction, 1(1):312–340, 2019.

这三类方法来建模，学习event sequences数据中的因果关系。但是，这些模型中的许多模型都依赖于数据分布的假设，这可能无法编码足够数量的特定领域知识。此外，因果模型的高度复杂性可能导致缺乏足够的可解释性来支持决策。
另一方面，事件序列数据还有如下特征会给因果分析带来更多挑战：

1. 高维度（high dimensionality）。事件序列数据集通常包含各种各样的事件类型。事件序列数据的高维度会显着增加因果分析结果的复杂性。
1. 高异质性（high heterogeneity）。以不同顺序发生的各种事件类型的序列导致个体之间的高度异质性。

这导致因果分析结果难以解释和验证。
![image-removebg-preview (3).png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618904515412-4d25bd82-3bea-4f7b-89b1-14ea52353a6b.png#align=left&display=inline&height=215&margin=%5Bobject%20Object%5D&name=image-removebg-preview%20%283%29.png&originHeight=215&originWidth=844&size=110094&status=done&style=none&width=844)
总结一下，挑战有三块:

1. 缺乏人的domain knowledge
1. 缺乏可解释性
1. 因果分析中的时序复杂度
#### 研究目标

- 将人类知识纳入因果关系分析中
- 增强可解释性
- 解决事件序列数据中的时序复杂性（temporal complexity）



##### 贡献

1. 系统。bottom-up的探索方式解决挑战3，同时帮助解决挑战2
1. 算法。作者设计了user-feedback的机制来增强因果分析算法，从而解决挑战1
1. 可视化。作者针对因果关系的分析，提出一些新的可视化方法，来消解数据的复杂度，解决挑战3
# SeqCausal
作者提出一个交互式的可视分析系统，叫SeqCausal，来帮助用户通过人机结合的方式，分析事件序列数据中的因果关系。
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618920491832-cf8c9d0c-d1be-42fa-a362-196376a64ef1.png#align=left&display=inline&height=513&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1025&originWidth=1766&size=690728&status=done&style=none&width=883)
作者的工作可以从两大块来看：a. 发现事件序列中因果关系的算法流程；b. 可视化系统的设计。
### 因果关系的算法流程
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618920906960-b837be0a-2889-43c2-9cbf-d3951f52e063.png#align=left&display=inline&height=296&margin=%5Bobject%20Object%5D&name=image.png&originHeight=591&originWidth=1926&size=224967&status=done&style=none&width=963)
因果分析算法的pipeline如上图，它分为三大步：

1. 训练Hawkes process模型来拟合数据集
1. 从模型参数中推断出因果关系
1. 结合用户的修改对模型做refine

这三个步骤中涉及到几个难点：
#### 1. Hawkes Processes建模
本文使用Hawkes Processes模型对事件序列进行建模。
作者直接使用了2016年提出的Granger causality analysis method of Hawkes processes。假设有V种类型的事件，则事件v在t时刻发生的概率定义为：
$$\lambda_{v}(t)=\mu_{v}+\sum_{v^{\prime}=1}^{V} \int_{0}^{t} \phi_{v v^{\prime}}(r) \mathrm{d} N_{v^{\prime}}(t-r)$$
其中$$\mu_{v}$$是一个常量，$$N_{v^{\prime}}(t-r)$$表示事件v'在t-r之前发生的次数，$$\phi_{v v^{\prime}}(r)$$表示历史事件v'对事件v的影响，它的定义如下：
$$\phi_{v v^{\prime}}(t)=\sum_{z=1}^{Z} a_{v v^{\prime}}^{z} \kappa_{z}(\stackrel{}{t)}$$
这里用了Z个采样函数$$\kappa_{z}(\stackrel{}{t)}$$，本文使用了高斯函数作为采样函数（算法的原文中$$\kappa_{z}(\stackrel{}{t)}$$也可以是其他的采样函数）。$$a_{v v^{\prime}}^{z}$$就是每个采样函数前的系数。有点像核密度估计，用多个正态分布去拟合目标函数，这里是拟合两个事件之间随事件变化的影响。Z的选择也是根据核密度估计里的Silverman's rule of thumb。
这里有个很关键的点：**参数**$$\boldsymbol{a}_{v v^{\prime}}=\left[a_{v v^{\prime}}^{1}, \ldots, a_{v v^{\prime}}^{Z}\right]^{\top}$$**其实就是v'对v的影响因子，如果里面的每一项都是0，意味着v'的出现对v没有任何影响。作者将$$\text { Strength }_{v v^{\prime}}=\frac{1}{Z} \sum_{z=1}^{Z} a_{v v^{\prime}}^{z}$$作为v'事件对v的影响大小，也就是v' -> v因果关系的强弱。可视化系统中对因果关系的可视化使用的就是这个strangth。**
为什么可以这样做？因为这里$$\kappa_{z}(\stackrel{}{t)}$$是正太分布，一直是正的。$$a_{v v^{\prime}}^{z}$$越大，也就意味着v'的发生会更能增加v发生的概率，这和Granger Causality的含义是一致的。
**
#### 2. 如何训练
上面的公式里，参数可以有两部分，常量$$\boldsymbol{\mu}=\left[\mu_{v}\right]_{v=1, \ldots, V} \in \mathbb{R}^{V}$$和影响系数$$\boldsymbol{a}=\left[a_{v v^{\prime}}^{z}\right]_{v, v^{\prime}=1, \ldots, V}^{z=1, \ldots, Z} \in \mathbb{R}^{V \times V \times Z}$$，作者将优化过程定义如下：
$$\operatorname{argmin}_{\boldsymbol{\mu}, \boldsymbol{a}} \quad-L+\alpha \sum_{v, v^{\prime}}\left\|\boldsymbol{a}_{v v^{\prime}}\right\|_{2}$$
前一项L其实是训练Hawkes process使用的似然函数，通过极大似然估计来优化模型（1971年提出的）$$L=\sum_{i=1}^{I}\left\{\sum_{m=1}^{M_{i}} \log \lambda_{v_{m}^{i}}\left(t_{m}^{i}\right)-\sum_{v=1}^{V} \int_{0}^{T_{i}} \lambda_{v}(r) d r\right\}$$。这里L前加了个符号作为loss的一部分。后一项是lasso正则化，主要用来约束$$a_{v v^{\prime}}^{z}$$的大小，后文中会有其他用途。
#### 3. 如何根据用户反馈refine模型
为了让可视化系统支持人机协作，还需要加入人的领域知识来优化模型。用户根据自己的知识，可以对模型给出的因果关系做两种操作：a. 用户认为A B之间没有因果关系，可以选择delete；b. 用户认为A B之间有因果关系，可以confirm。随后，模型会根据用户的操作，进一步优化模型。
模型是如何“记住”用户的操作的？作者主要在loss里做了手脚：

1. 当用户delete了一对事件的因果关系，模型中相应的$$a_{v v^{\prime}}^{z}$$会直接置为0，不再参与训练。
1. 当用户confirm了一对事件的因果关系，对应的$$a_{v v^{\prime}}^{z}$$将不再参与lasso正则化，这意味着$$a_{v v^{\prime}}^{z}$$的大小不再受正则化约束，更容易增大。



以上就是因果关系的计算和优化过程，接下来着重介绍系统的设计。

### 可视分析系统
作者将可视分析系统的界面分为三大部分：

- Causal Exploration
- Causal Verificatoin
- Causal Comparison

我们来结合case study看。case study使用了MIMIC数据集，数据集包含四万多人的电子病历。作者针对肺炎数据，从中识别出120种事件，主要分为药治疗、体检指标。
#### 1. Causal Exploration
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618971502366-faa45597-ea42-48d4-99dd-e207fe7db540.png#align=left&display=inline&height=424&margin=%5Bobject%20Object%5D&name=image.png&originHeight=847&originWidth=1893&size=619130&status=done&style=none&width=946.5)
首先用户通过上图中的1对数据进行筛选，这里选择了肺炎数据，筛选了事件、性别、年龄。相应的数据会显示在2中。系统会根据当前选择的数据，训练hawkes process模型，从模型中的参数中提取出事件的因果关系，再将这些事件的因果关系通过下图node-link的方式绘制出来。为了让图布局效果更好，作者提出了一种基于Stress Majorization的布局算法。
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618971841041-e7667ffe-f695-4d2f-95f2-0cedf6e81e7c.png#align=left&display=inline&height=377&margin=%5Bobject%20Object%5D&name=image.png&originHeight=753&originWidth=1307&size=262492&status=done&style=none&width=653.5)
具体到case中，如下图。最下面的O2A是O2含量上升，是肺炎好转的信号。向上推理可以看到CO2的降低（TCO2）是O2含量上升的直接原因。进一步向上寻因，可以发现Metr、Levo、Cafe、Aztr这四种治疗方法能够有效治疗肺炎。
医生进一步检查了pH值异常（PHA）的原因，并在包括TCO2，O2A和pH值在内的三个指标之间发现了循环因果关系。由于这种周期性的因果关系，患者的病情将不断恶化
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618975996884-8b58be7e-6f64-4a26-8ba9-8f1bf60c2d12.png#align=left&display=inline&height=314&margin=%5Bobject%20Object%5D&name=image.png&originHeight=628&originWidth=296&size=67475&status=done&style=none&width=148)
在事件数量很多的情况下，图可能会很复杂。作者也提出了一种用户驱动的探索流程：

1. 用户通过点击右上角的加号，选择要关注的结果事件。系统会仅绘制出该事件。

![image-removebg-preview (4).png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618972377606-cca40444-643c-4428-9ab0-debab24d588e.png#align=left&display=inline&height=52&margin=%5Bobject%20Object%5D&name=image-removebg-preview%20%284%29.png&originHeight=52&originWidth=147&size=6892&status=done&style=none&width=147) -> ![image-removebg-preview (5).png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618972515304-62c73ec4-728a-4b39-a080-ec009ac9fa18.png#align=left&display=inline&height=51&margin=%5Bobject%20Object%5D&name=image-removebg-preview%20%285%29.png&originHeight=51&originWidth=147&size=6690&status=done&style=none&width=147)

2. 用户双击感兴趣的事件，逐层展开相关的因果关系。



##### 信息编码
颜色深浅代表因果关系的强度，外环的弧长代表数据集中，因果关系中两个事件连续出现的比例。


#### 2. Causal Verification
为了帮助用户验证因果关系的有效性，作者设计了验证视图：
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618975658287-31c1f47f-614a-4fdc-86f4-f75d65329d76.png#align=left&display=inline&height=207&margin=%5Bobject%20Object%5D&name=image.png&originHeight=414&originWidth=896&size=146335&status=done&style=none&width=448)
也就是图中的4。用户通过选择一对因果关系A->B来显示这个视图。图中最左边的白色条带表示因，最右边的灰色条带代表果。作者将与因果关系A->B相关的事件序列分成三类：

1. 序列经过原因A，但没有遇到结果B，即A->?，对应下图中的绿色
1. 同时包含A和B的序列，即序列中有A->B，对应下图中的橙色
1. 序列中包含结果B，但不存在原因A，即?->B，对应下图中的蓝色

![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618980929582-991a0793-2c83-4cab-8545-fe080d26b5cb.png#align=left&display=inline&height=271&margin=%5Bobject%20Object%5D&name=image.png&originHeight=541&originWidth=1407&size=199519&status=done&style=none&width=703.5)
同时，上图中色带的高度与相应序列数据的个数成正比。
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618982883371-cb14d20e-a8bf-4892-ab17-7b734b53c3eb.png#align=left&display=inline&height=325&margin=%5Bobject%20Object%5D&name=image.png&originHeight=649&originWidth=1348&size=110368&status=done&style=none&width=674)
除此之外，作者还在图中添加了其他的潜在原因。潜在原因按其平均发生时间从左到右垂直对齐并在水平方向上按顺序排列，并通过重排和聚合显示成上图c中的效果。
当用户对因果关系进行验证之后，可以回到因果关系的探索视图，对相应的边进行confirm或者delete操作，如下图
![3333.gif](https://yuque.zju.edu.cn/images/yuque/0/2021/gif/314/1618984205970-b29186ad-eaf9-43d8-aee1-d5f5ec53592d.gif#align=left&display=inline&height=808&margin=%5Bobject%20Object%5D&name=3333.gif&originHeight=808&originWidth=1400&size=1114557&status=done&style=none&width=1400)
用户完成对因果关系的确认后，点击右上角的完成按钮更新模型。模型会按照上文算法流程中的feedback机制对模型做refine。
每次模型更新，系统会对模型的性能进行评估，评估的指标有三个：

1. regression likelihood，即训练时loss里的L
1. Bayesian Information Criterion（BIC）
1. P-value

这些指标在下图中的模型诊断面板中编码出来：

- 圆圈代表某次模型更新
- x轴模型更新的次数
- y轴regression likelihood的大小
- 圆圈上竖直的线表示regression likelihood的标准差
- 圆圈的颜色编码BIC的变化，绿色表示增大，红色表示降低

![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618984590032-1d4695b7-6ee0-403e-8d14-c45487b49616.png)                                       
回到MIMIC数据集上的case。医生在系统中分析时结合自己的领域知识，确认了pH异常会导致BUN值增加，确认了CO2，PHA和TCO2之间的循环关系。模型根据用户反馈进行更新，整体性能提升：
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618985921686-292161e3-e951-4bd0-af23-8731c81ead1d.png#align=left&display=inline&height=169&margin=%5Bobject%20Object%5D&name=image.png&originHeight=338&originWidth=582&size=52495&status=done&style=none&width=291)


#### 3. Causal Comparison
用户在对某个筛选条件下的数据子集探索后，可以将因果关系的结果保存到历史记录，即下图中的5。系统提供因果关系对比面板，来让用户对比两个不同数据自己的因果关系差异，如下图6。
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618985976741-b46857f7-4b42-4449-8c97-5b883d02ce95.png#align=left&display=inline&height=508&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1015&originWidth=877&size=219336&status=done&style=none&width=438.5)
##### 信息编码
![image-removebg-preview (7).png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618986362130-9d9eada3-2a51-4696-9594-4efd2bfa6241.png#align=left&display=inline&height=359&margin=%5Bobject%20Object%5D&name=image-removebg-preview%20%287%29.png&originHeight=359&originWidth=695&size=117103&status=done&style=none&width=695)
作者使用邻接矩阵来比较两组中所有因果关系的发生。矩阵的行表示因，而列表示果。每个单元划分为外部区域和内部区域，背景色饱和度分别代表第一组和第二组中的因果强度。


在case里，医生通过对比面板发现，“Aztreonam治疗->TCO2下降”这个因果关系在老年人和中年人群体中有明显差异——中年人中这个因果关系要强于老年人。通过verification视图发现确实如此，医生推测可能Aztreonam治疗在老年人身上效果不好。
![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618986538469-d20513e6-9a51-4503-a7c5-43f53f40a3a9.png#align=left&display=inline&height=441&margin=%5Bobject%20Object%5D&name=image.png&originHeight=883&originWidth=556&size=130373&status=done&style=none&width=278)


# Evaluation
除了穿插在上文中的case study，作者还做了针对user-feedback机制的性能评估。
作者使用了3个指标：

- negtive log-likelihood，即loss中的第一项
- BIC
- Area Under ROC，评估Accuracy

![image.png](https://wangzhiyong-files.oss-cn-shanghai.aliyuncs.com/markdown/1618986988233-b7523a21-12c2-447c-8303-93348de68c60.png#align=left&display=inline&height=124&margin=%5Bobject%20Object%5D&name=image.png&originHeight=248&originWidth=894&size=74662&status=done&style=none&width=447)
该结果表明，用户反馈机制总体上提高了因果关系分析模型的拟合优度和分析结果的准确性。
# 总结和联想
针对事件序列数据，作者提出了user-feedback的算法机制，并设计了一套可视化系统，来将人的domain knowledge融入到因果分析中。实验表明，他们的成果成功地将专家经验融入到可视分析中，并提高了模型的准确度。
这篇是典型的将机器智能和人的智能结合在一起的文章。本工作是将机器学习模型的部分参数进行解释，然后可视化放进系统里，用户可以对参数进行修改。修改后，模型通过一些机制（如让用户给定的参数直接不参与训练），让模型在此基础上，进一步优化，从而达到人机协作。
之前我报告过一篇叫collaborative semantic inferrence（CSI）的文章，也是相似的做法，只是那篇里面是针对推理过程的：在模型里设置一些可解释的潜变量，模型在推理时，用户能够通过控制这些潜变量来决定模型的推理方式。
这两篇在实现人机协作时有一些共同点：

1. 模型中都有可解释的部分（人理解模型的前提）。本篇是对参数做可解释地解读，CSI是在模型中埋可解释的潜变量。
1. 通过对可解释的部分进行调节，反作用于模型。本篇是将可解释部分固定或释放，继续训练模型，CSI是通过改变潜变量直接影响模型推理。

应该说很多机器学习模型（尤其是传统模型）都具备可解释的部分，按照这个思路，这些模型都可以做人机协作。
