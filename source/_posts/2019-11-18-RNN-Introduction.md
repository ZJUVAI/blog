---
title: 循环神经网络 (Recurrent Neural Network, RNN)
tags: ["报告", "主题报告"]
date: 2019-11-18
author: 张建伟
mail: zjw.cs@zju.edu.cn
mathjax: true
---

## 1. 循环神经网络 (Recurrent Neural Network, RNN)

在机器学习中, 数据表示为 $n$ 维特征向量 $\mathbf{x}\in\mathbb{R}^n$ 或 $h\times w$ 维特征矩阵(如图片), 多层感知机 (multilayer perceptron, MLP) 和卷积神经网络 (convolutional neural network, CNN) 可以提取数据中的特征以进行分类回归等任务.
但通常的 MLP 或 CNN 处理的数据通常认为是独立同分布的, 因此当数据之间存在关联关系时, 这类模型则无法很好的编码数据间的依赖关系, 导致模型的表现较差. 一种典型的数据间依赖关系就是*时序关系*. 比如话说一半时对方可能就知道了你的意思, 一句话中的代词"他"指代的目标需要分析上下文后才能得到. 时序数据如下图所示, 一列向量则表示一个字的编码.

![时序数据](https://www.jarvis73.cn/images/2018-1-24/time-series.png)

循环神经网络的提出就是为了解决数据中这种典型的时序依赖关系. RNN 是内部包含循环的神经网络 (普通 CNN 不包含循环), RNN 的一个循环单元如下图所示[^1].

![RNN 的循环单元](https://www.jarvis73.cn/images/2018-1-24/RNN-rolled.png)

其中 $x_t$ 表示输入序列中时刻 $t$ 时的值, $h_t$ 为该层在时刻 $t$ 的输出. 方块 $A$ 是一个操作符, 把前一时刻的输出 $h_{t-1}$ 和当前时刻的输入 $x_t$ 映射为当前时刻的输出. 注意, $h_t$ 通常扮演两个角色, 既是循环单元在当前时刻的输出, 又是当前时刻循环单元的*状态*. 公式表示如下:

$$
h_t = \sigma(W_{hx}x_t + W_{hh}h_{t-1}).
$$

其中 $W$ 为权重参数, $\sigma$ 表示激活函数, 常用的是 $\tanh(\cdot)$ 函数, 可以把输出的值域控制在 $[-1, 1]$ 之间, 避免在循环过程中不收敛. 我们可以沿着时间轴把上面的循环单元展开, 更加直观.

![RNN 循环单元展开示意图](https://www.jarvis73.cn/images/2018-1-24/RNN-unrolled.png)

循环神经网络可以由多层循环单元堆叠而成, 前一个循环单元的输出作为下一循环单元的输入, 如下图所示.

<!-- 在训练过程中, ... -->

![多层循环单元堆叠](https://www.jarvis73.cn/images/2018-1-24/multilayer-RNN.png)

RNN 的输入通常表示成**嵌入 (embedding)**的形式, 即构造一个**查询表 (lookup table)**, 把输入序列的每个时刻的特征向量通过查询表转为一个等长的向量. 从而一个序列的形状变为 `[num_time_steps, embedding_size]`.

### 1.1 RNN 的应用

RNN 可以根据输入序列的长度和输出序列的长度分为三大类.

-   多对一: 常用于情感分析, 文本分类
-   一对多: Image Caption
-   多对多: 机器翻译
-   一对一: 退化为 MLP

### 1.2 RNN 的局限性

RNN 也存在一些缺陷:

-   RNN 可以很好的学习序列中邻近时间步数据点(短期)之间的关系, 但对于长期依赖会变得不稳定.
-   RNN 可以把固定长度的输入序列映射到指定长度的输出序列, 但不能动态地根据输入决定输出多长的序列.

而 LSTM 和 Encoder-Decoder 的提出解决了这两个问题.

## 2. 长短期记忆 (Long Short Term Memory, LSTM)[^1]

前面提到, RNN 对于长期依赖经实验表明是不稳定的. 对于短序列, 如一个句子: "The clouds are in the ()", 括号中预测一个词, 那么很容易根据该词前面的 clouds 和 in 推断出填 sky. 但是对于长序列, 如 "I grew up in France ... I speak fluent ()", 句子中的省略号包含了大量其他信息, 此时最后括号中的词应当根据开头的 France 推断为 French, 但中间大量的无用语句会稀释前期的信息, 导致 RNN 无法正确预测最后的词. 而 Hochreiter & Schmidhuber 提出的 LSTM [^6] 正是解决该问题的.

LSTM 和通常的 CNN 一样为一个循环单元的结构, 但是与 RNN 仅有一个 tanh 激活层不同, LSTM 中包含了更复杂的四层网络的结构设计, 并且四层网络相互耦合, 如下图所示.

![LSTM 循环单元展开示意图](https://www.jarvis73.cn/images/2018-1-24/LSTM.png)

上图中的圆角矩形框, 操作符, 分支箭头的含义如下图所示.

![图例](https://www.jarvis73.cn/images/2018-1-24/LSTM2-notation.png)

下面详细介绍 LSTM 单元的内部结构.

### 2.1 LSTM 的核心思想

LSTM 相比于 RNN, 关键在于引入了单元状态(state) $C$ —— 横穿下图顶部的直线.

![单元状态](https://www.jarvis73.cn/images/2018-1-24/LSTM3-C-line.png)

LSTM 可以通过**门(gate)**来控制向单元状态中增加信息或减少信息. 门由一个 $sigmoid$ 函数和一个乘法运算符组成, 如下图所示.

![门](https://www.jarvis73.cn/images/2018-1-24/LSTM3-gate.png)

$sigmoid$ 层输出的值在 $[0, 1]$ 之间, 控制了信息的通过量. 越接近 0, 则表明不允许信息通过(从而形成*遗忘*); 越接近 1, 则表明允许信息全部通过(从而形成*记忆*).

### 2.2 LSTM 单元解析

LSTM 单元在每个时间步需要注意三个向量:

-   输入的特征向量 $x_t$
-   上一步输出的特征向量 $h_{t-1}$
-   上一步结束后的单元状态 $C_{t-1}$

要注意三个向量是相同的长度.

**遗忘门(forget gate).** 每循环一步时, 首先根据上一步的输出 $h_{t-1}$ 和当前步的输入 $x_t$ 来决定要遗忘掉上一步的什么信息(从单元状态 $C_{t-1}$ 中遗忘). 因此只需要计算一个遗忘系数 $f_t$ 乘到单元状态上即可. 如下图所示, 公式中的方括号表示 $concat$ 操作.

![遗忘门](https://www.jarvis73.cn/images/2018-1-24/LSTM3-focus-f.png)

**输入门(input gate).** 这一步来决定当前新的输入 $x_t$ 我们应该把多少信息储存在单元状态中. 这部分有两步, 首先一个输入门计算要保留哪些信息, 得到过滤系数 $i_t$, 然后使用一个全连接层来从上一步的输出 $h_{t-1}$ 和当前步的输入 $x_t$ 中提取特征 $\tilde{C}_t$. 如下图所示.

![输入门](https://www.jarvis73.cn/images/2018-1-24/LSTM3-focus-i.png)

**新旧信息合并.** 计算好了遗忘系数, 输入系数, 以及新的要记忆的特征, 现在就可以在单元状态 $C_{t-1}$ 上执行遗忘操作 $f_t\ast C_{t-1}$ 和记忆操作 $+i_t\ast\tilde{C}_t$. 如下图所示.

![新旧信息合并](https://www.jarvis73.cn/images/2018-1-24/LSTM3-focus-C.png)

**输出门(output gate).** 最后我们要决定输出什么信息了. 这需要从当前的单元状态 $C_t$ 来获取要输出的信息. 但显然我们并不会在这一个时间步输出所有记忆的信息, 而是只要输出当前需要的信息, 因此我们用一个输出门来过滤输出的信息, 过滤系数为 $o_t$. 此外我们希望输出的特征的取值能够介于 $[-1, 1]$ 之间, 因此使用一个 $tanh$ 函数把单元状态 $C_t$ 映射到相应的范围, 最后乘上过滤系数得到当前步的输出. 如下图所示.

![输出门](https://www.jarvis73.cn/images/2018-1-24/LSTM3-focus-o.png)

### 2.3 LSTM 单元变体

**变体一.** [^7]让所有的门控单元在输出门控系数的时候都可以"看到"当前的单元状态. 如下图所示.

![让门控单元可以看到单元状态](https://www.jarvis73.cn/images/2018-1-24/LSTM3-var-peepholes.png)

**变体二.** 让遗忘门的遗忘系数 $f_t$ 和输入门的输入系数 $i_t$ 耦合, 即令 $i_t = 1-f_t$, 从而同时做出哪些信息遗忘以及哪些信息记忆的决策. 这个变体可以让新的有用的记忆"覆盖"老的无用的记忆. 如下图所示.

![遗忘系数和记忆系数耦合](https://www.jarvis73.cn/images/2018-1-24/LSTM3-var-tied.png)

**变体三(GRU).** [^8]第三种变体更为有名, 称为**门控循环单元(Gated Recurrent Unit, GRU)**. 下一节介绍.

**其他参考:** 其他可以参考如下文献:

-   [Depth Gated RNNs](http://arxiv.org/pdf/1508.03790v2.pdf)
-   [Variants comparision](http://arxiv.org/pdf/1503.04069.pdf)
-   [Ten thousand RNN architecture tests](http://jmlr.org/proceedings/papers/v37/jozefowicz15.pdf)

## 3. 门控循环单元(Gated Recurrent Unit, GRU)

GRU[^8] 是一种比 LSTM 稍简单一些的循环单元. GRU 把 LSTM 中的隐藏状态 $h$ 和单元状态 $C$ 合并为单个的隐藏状态. 如下图所示.

![门控循环单元 (GRU)](https://www.jarvis73.cn/images/2018-1-24/LSTM3-var-GRU.png)

**更新门(update gate).** 更新门系数 $z_t$ 控制了 $h_{t-1}$ 中保存的信息(如长期记忆)在当前步保留多少. $z_t$ 接近 0 时上一步的隐藏状态中的信息 $h_{t-1}$ 得以保留, 新输入的信息 $\tilde{h}_t$ 会被忽略; $z_t$ 接近 1 时则丢弃已有的信息, 并填入新输入的信息.

**输入信息的加工.** 当前步输入的信息需要加工后才能合并到隐藏状态 $h_t$ 中. 输入信息加工时需要参考上一步的隐藏状态 $h_{t-1}$ 来决定哪些信息有用, 哪些没用. 加工后的信息用 $\tilde{h}_t$ 表示.

**重置门(reset gate).** 重置门系数 $r_t$ 控制了在加工输入信息的时候使用上一步的隐藏状态中的哪些信息. $r_t$ 接近 0 时新输入的信息占主导地位, 说明当前步的输入包含的信息与前面的信息关联性很小; $r_t$ 接近 1 时新输入的信息和前面的长期信息有较大关联性, 需要综合考虑来产生当前步加工后的信息.

## 4. 编码-解码器 (Encoder-Decoder)

编码-解码器模型是为了实现 $n\rightarrow m$ 序列映射的模型框架. 这类模型也称为 Sequence to Sequence(seq2seq). 编码器只负责处理输入序列, 并形成输入序列的特征向量后送入解码器. 为了清楚, 我们用公式来表示这个过程[^5]. 对于输入序列 $X=\\{x_1, x_2, \cdots, x_S\\}$ 和期望的输出序列 $Y=\\{y_1, y_2, \cdots, y_T\\}$, 我们使用 RNN 模型对其进行建模, 形成一个条件概率 $P(Y\vert X)$, 使用链式法则解耦如下:

$$
P(Y|X) = \prod_{t=1}^TP(y_t\lvert y_1, y_2, \cdots, y_{t-1}, X).
$$

那么该 RNN 模型就是一个编码器, 在时刻 $s$ 的状态通过下式计算:

$$
h_s = f_{enc}(h_{s-1}, x_s).
$$

编码器的示意图如下图[^2]所示:

![编码器. 输出的语义向量可以通过不同的计算公式构造, 形成不同的模型结构](https://www.jarvis73.cn/images/2018-1-24/encoder.png)

解码器 RNN 每个时间步使用前一步的输出 $y_{t-1}$ 和当前状态 $g_t$ 产生一个输出 $y_t\in Y$:

$$
\begin{align}
g_1 &= h_s \\
g_t &= f_{dec}(g_{t-1}, y_{t-1})
\end{align}
$$

每个时间步的输出概率通过一个线性层和一个 softmax 函数得到:

$$
P(y_t\lvert y_1, y_2, \cdots, y_{t-1}, X) = Softmax(Linear(g_t)).
$$

使用解码器对语义向量解码. 如果把语义向量只输入解码器的第一个循环时间步, 则形成了 Cho et al.[^3] 提出的结构.

![解码器-1](https://www.jarvis73.cn/images/2018-1-24/decoder-1.png)

如果把语义向量在每一个时间步都输入解码器, 则形成了 Sutskever et al.[^4] 提出的结构.

![解码器-2](https://www.jarvis73.cn/images/2018-1-24/decoder-2.png)

### 4.1 编码-解码器模型的局限性

-   信息的丢失: 整个时间序列只能压缩为一个固定长度的语义向量
-   不合理性: seq2seq 的任务中输入序列 $\\{x_0, x_1, \dots, x_{t−1},  x_𝑡, x_{t+1},\dots \\}$ 中的每个元素对所有 $y_s$ 的贡献度是相同的

例如: The animal didn't cross the street because **it** was too tired. 在这句话中, 人是通过综合整句话的信息来判断单词 it 指代的是 the animal, 从而翻译时 the animal 应该对 it 的影响更大.

人们提出了注意力模型来解决普通编码-解码器模型的问题.

## 5. 注意力机制 (Attention Mechanism)[^11]

### 5.1 什么是注意力?

心理学中对注意力的解释是:

> **Attention** is the behavioral and cognitive process of selectively concentrating on a discrete aspect of information, whether deemed subjective or objective, while ignoring other perceivable information.

注意力是把有限的资源集中在更重要的目标上. 注意力机制的两个要素:

-   决定输入信息的哪部分是重要的
-   把资源集中分配到重要的信息上

沿用编码-解码器模型的结构, 注意力机制通过引入一组归一化的系数 $\\{\alpha_1, \alpha_2, \dots, \alpha_n \\}$ 来对输入的信息进行选择, 来解决编码-解码器的不合理性. 这里输入的信息就是指输入序列在 RNN 中的单元输出 $\mathbf{h}_s$. 归一化的系数 $\alpha_s$ 用来决定输入信息的重要性, 是编码器输出时对单元输出加权求和系数. 注意力机制在计算不同时间步的输出时, 实时构造编码器输出的语义向量 $\mathbf{c}_t$, 从而解决了普通编码-解码器信息丢失的问题. 注意力机制如下图所示.

![注意力机制](https://www.jarvis73.cn/images/2018-1-24/attention-1.png)

下面讨论加权系数是如何计算的. 考虑到加权系数要反映**输入信息**在当前**时间步**上的重要性, 因此需要把输入信息和时间信息结合起来计算加权系数. 因此通常使用一个 MLP 接收输入信息 $\mathbf{h}\_1, \mathbf{h}\_2, \dots, \mathbf{h}\_n$ 和解码器上一时刻的状态 $\mathbf{s}\_{t-1}$ 作为输入, 输出归一化的加权系数 $\alpha_{t1}, \alpha_{t2}, \dots, \alpha_{tn}$. 如下图所示.

![注意力机制](https://www.jarvis73.cn/images/2018-1-24/attention-2.png)

注意: 编码-解码器的结构只是注意力机制的一个载体, 注意力机制的核心在于加权系数, 因此可以用于非 RNN 的结构.

### 5.2 自注意力 (Self-Attention)

自注意力是一个神经网络模块, 它仍然是使用了注意力机制, 与编码-解码器结构不同的是, 自注意力模块只使用输入的信息计算加权系数, 而不需要上一个时间步的信息, 因此可以实现更大规模的并行计算. Google 在 2017 年提出的 Transformer 实现了可并行的自注意力模块[^9]. 其结构如下图所示.

![语言翻译任务中的自注意力](https://www.jarvis73.cn/images/2018-1-24/self-attention.png)

自注意力模块工作方式如下[^10]:

-   输入的序列首先转化为相同长度的 embedding
-   创建三个矩阵: 查询矩阵 (Query), 键矩阵 (Key), 值矩阵 (Value)
-   使用这三个矩阵把每个单词的 embedding 映射为三个稍短的特征向量, 分别代表了当前单词的查询向量, 键向量和值向量.
-   比如我们要计算单词 Thinking 关于句子中其他单词的注意力时, 使用 Thinking 的查询向量依次与句子中所有单词 (包括 Thinking) 的键向量做点积来计算相似度, 并对相似度进行归一化得到加权系数 (这是 attention 的核心部分).
-   加权系数乘到所有单词的值向量上来得到单词 Thinking 经过自注意力模块后输出的特征向量, 这个特征向量中可以看作包含了翻译任务重对准确翻译 Thinking 所需要的信息, 而不包含其他信息 (其他信息在翻译其他词时可能有用, 但在翻译 Thinking 时无用).

## References

[^1]:
    **Understanding LSTM Networks -- Colah's blogs** <br />
    [[link]](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) OnLine.

[^2]:
    **详解从 Seq2Seq 模型、RNN 结构、Encoder-Decoder 模型 到 Attention 模型** <br />
    [[link]](https://caicai.science/2018/10/06/attention%E6%80%BB%E8%A7%88/) OnLine.

[^3]:
    **Learning phrase representations using RNN encoder-decoder for statistical machine translation** <br />
    Cho K, Van Merriënboer B, Gulcehre C, et al. <br />
    [[link]](https://arxiv.org/abs/1406.1078) In arXiv preprint arXiv:1406.1078, 2014.

[^4]:
    **Sequence to Sequence Learning with Neural Networks** <br />
    Ilya Sutskever, Oriol Vinyals, Quoc V.Le. <br />
    [[link]](http://papers.nips.cc/paper/5346-sequence-to-sequence-learning-with-neural-networks) In Advances in neural information processing systems. 2014: 3104-3112.

[^5]:
    **Order Matters: Sequence to Sequence for Sets** <br />
    Vinyals, Oriol, Samy Bengio, and Manjunath Kudlur. <br />
    [[link]](http://arxiv.org/abs/1511.06391.) In ArXiv:1511.06391, November. 2015.

[^6]:
    **Long short-term memory** <br />
    Hochreiter S, Schmidhuber J. <br />
    [[link]](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.676.4320&rep=rep1&type=pdf) In Neural computation, 1997, 9(8): 1735-1780.

[^7]:
    **Recurrent nets that time and count** <br />
    Gers F A, Schmidhuber J. <br />
    [[link]](ftp://ftp.idsia.ch/pub/juergen/TimeCount-IJCNN2000.pdf) In Proceedings of the IEEE-INNS-ENNS International Joint Conference on Neural Networks. 2000.

[^8]:
    **Learning phrase representations using RNN encoder-decoder for statistical machine translation** <br />
    Cho K, Van Merriënboer B, Gulcehre C, et al. <br />
    [[link]](https://arxiv.org/pdf/1406.1078) In arXiv preprint arXiv:1406.1078, 2014.

[^9]:
    **Attention is all you need** <br />
    Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Lukasz Kaiser, Illia Polosukhin <br />
    [[link]](https://arxiv.org/abs/1706.03762) Advances in neural information processing systems. 2017: 5998-6008.

[^10]:
    **The Illustrated Transformer** <br />
    Jay Alammar <br />
    [[link]](https://jalammar.github.io/illustrated-transformer/) OnLine.

[^11]:
    **Neural machine translation by jointly learning to align and translate** <br />
    Dzmitry Bahdanau, Kyunghyun Cho, Yoshua Bengio <br />
    [[link]](https://arxiv.org/abs/1409.0473) arXiv preprint arXiv:1409.0473, 2014.
