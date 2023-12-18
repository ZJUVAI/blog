---
title: "The Attraction Effect in Information Visualization"
tags: ["论文评述", "报告"]
date: 2017-06-09
author: 王叙萌
mail: wangxumeng@zju.edu.cn
---

来源：TVCG (InfoVis) 2017

作者：Evanthia Dimara, Anastasia Bezerianos, and Pierre Dragicevic

## 一、动机

1. 存在诱饵效应和不对称效应。
2. 在信息决策中，完备信息不一定推得正确的决定。
3. 吸引力影响很重要，没有被充分理解。

## 二、相关工作

1. 信息可视化中支持决策的可视化系统，比如磁铁与灰尘等。

2. 纯信息方法没有给人留下干预空间且决策过程中往往有主观偏好。

3. 感知存在偏差。

4. 探究吸引力影响的典型组合：竞争者、对象和诱饵。其中诱饵只被对象主导，对象和竞争者之间互不主导。诱饵的存在可能会使用户倾向于选择对象而不是竞争者.

### 三、第一个实验

1. 实验目的：重做吸引力影响的测试，并探究散点图的效果。

2. 实验设计：根据多样化和整洁度两个数值属性选出最合适的健身房。

3. 实验任务：（大写字母代表的个体主导且仅主导小写字母代表的个体）从下列组合中选择最合适的健身房：{C, V}，{C, V, c}，{C, V, v}。

4. 被试筛选：基于完成时间、选择公正性、是否掌握表格和散点图的用法进行筛选。

5. 实验假设：

    用表格分析或用散点图分析时，选一方对象的比例：

    1. 添加一方诱饵>不添加诱饵
    2. 添加一方诱饵>添加另一方诱饵

6. 实验结果

[![img](http://www.cad.zju.edu.cn/home/vagblog/wp-content/uploads/2017/06/%E5%9B%BE%E7%89%871.png)](http://www.cad.zju.edu.cn/home/vagblog/wp-content/uploads/2017/06/%E5%9B%BE%E7%89%871.png)

可以观察到，除了在用表格进行实验时对整洁度加诱饵起了反向作用以外，实验结果符合预期。该异常现象作者没有给出明确解释，可能是由于被试都比较熟悉健身房，因此有所偏好。

## 三、第二个实验

1. 实验目的：探究多点散点图的效果。
2. 加点方法：

    1. 加不被主导的个体（因打破被试选择的二分比例弃用）
    2. 加诱饵
    3. 加分散注意力的点

3. 实验设计：根据中奖概率和奖金选择彩票。
4. 改动：对比互换对象、竞争者身份的一组实验；散点图有高亮显示左边的交互。
5. 实验假设：有正向影响。
6. 实验结果：

[![img](http://www.cad.zju.edu.cn/home/vagblog/wp-content/uploads/2017/06/%E5%9B%BE%E7%89%872.png)](http://www.cad.zju.edu.cn/home/vagblog/wp-content/uploads/2017/06/%E5%9B%BE%E7%89%872.png)

7. 量化影响：一直选择有诱饵一方的（左下角黑框 23%） – 一直选择没有诱饵一方的（右上角黑框 8%） = 15%。

## 四、讨论

1. 可视化中吸引力影响依然存在。
2. 需要消除偏好的设计，比如高亮 skyline。
3. 局限：这种偏见可能有积极影响；没有考虑现象产生的原因；实际数据集与实验用数据集有差别。
4. 未来工作：贴近实际应用场景；考虑其他可视化方法。

## 五、总结

1. 优点：问题贴近实际应用；设计考虑周全（比如对被试的过滤）；问题大，切入点小。

2. 缺点：两个实验设计都参考了之前的工作，创新少；待解决问题较多。

3. 启发：需要在可视化中注意认知影响。