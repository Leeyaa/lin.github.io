---
title: Some usual Metrics in Recommender System
date: 2018-12-27 20:57:30
categories:
- Recommender System
---

推荐系统有很多常见的评价指标，本文对常见的一些指标做一个总结

<!-- more -->


## topN与评分预测、CTR prediction区别

### topN
1. pair-wise或list-wise，用户相对于i更喜欢j。**Top-N推荐我们采pair-wise的式来对用户进行偏好的偏序建模，即考虑每个用户对于物品的喜好序关系。**
2. 最终的结果是topN的排序，而不是评分
3. 隐式反馈的情况比较多
4. 一般用MAP，NDCG等评价指标
5. 更符合实际情况一些

### 评分预测
1. point-wise
2. 预测出的评分如果全是5分，并没有办法推荐出最好的N个来
3. 显示反馈的情况比较多

### CTR
1. 可以计算出准确概率

## Evaluation Metrics 
### accuracy(准确率) 
-  $number of correct predictions/total num of predictions$ 确结果所占的分比
-  对于二元分类，也可以表示为：$(TP+TN)/(TP+TN+FP+FN)$
-  虽然准确率是一种非常直观且常见的metric，但是有一些重大的缺陷
-  如：分类不平衡，即，正类别或负类别极其罕见，**当您使用分类不平的数据集（比正类别标签和负类别标签的数量之间存在明显差异）时，单准确率一项并不能反映全面况。（肿瘤的例子）**

### [classification threshold - Metrics](https://developers.google.cn/machine-learning/crash-course/classification/true-false-positive-negative)

- 为了将逻辑回归值映射到二元类别，您必须指定分类阈值（也称为判定阈值）。果值高于该阈值，则表示“垃圾邮件”；如果值低于该阈值，则表示“非垃圾邮件”。
- **'TP'第一位表示是否预测对了，第二位表示做的预测是正例还是负例**

### 精确率 - 提高threshold
- $TP/TP+FP$   
- 当模型的预测为“正”类别时，预测正确吗？
- 直觉：模型是否过于频繁地提醒说“狼来了”？ ， 精确率越高，预测的恶肿瘤中，预正确率越高，预测的垃圾邮件中，垃圾邮件的概率越高。精确越低，预测的恶性肿瘤中，良性误认为恶性的概率就越高，垃圾邮件中，工作邮件预测为垃圾邮件的概率越高。
- 改变对阈值的影响：
    - 变高：阈值变高
    - 变低：阈值变低
    - 精确率并不一定会随着阈值的提高单调递增

### F1 值：为了折中精确率与召回率的结果，引入了F1值
1. $F1 = (2*recall*precision)/(recall+precision)$ 当recallprecision同时变高的时候，F1值才会变高

### 召回率：降低threshold
- $TP/TP+FN$
- 在所有正类别中，模型正确地识别出了多少？
- 直觉：是否漏掉了任何“狼来了”的情况？，召回率越低，漏掉的垃圾邮件多，漏掉的性肿瘤越多。召回率越高，漏掉的垃圾邮件越少，漏掉的恶性瘤越少。
- 改变对阈值的影响
    - 变高：阈值变低
    - 变低：阈值变高

### 精确率和召回率
1. 精确率和召回率**一般**是此消彼长的状态
2. 垃圾邮件预测 与 肿瘤预测是两个相反的例子
    - 垃圾邮件预测对召回率要求低，因为要求不能将工作邮件误判为垃圾件
    - 肿瘤预测中对召回率要求高，因为不能漏判恶性肿瘤
3. 阈值降低：TP和FP都会增加，FN,TN都会减小，所以召回率变高，精确**可能**变低 

### ROC
1. ROC 曲线用于绘制**采用不同分类阈值**时的 TPR 与 FPR。降低分类值会导致将更多样本归为正类别，从而增加假正例和真正例的个数，增加FP和TPR

### AUC
1. 一种会考虑所有可能分类阈值的评估指标。
2. AUC还有另一种解释，就是测试任意给一个正类样本和一个负类样本，正样本的score多大的概率大于负类样本的score。（BPR文章中AUC用到的式）
3. 计算方法
    -  第一种方法：AUC为ROC曲线下的面积，那我们直接计算面积可得。积为一个个的梯形面积之和，计算的精度与阈值的精度有关。
    - 第二种方法：根据AUC的物理意义，我们计算正样本score大于负样的score的率。取$N*M$（N为正样本数，M为负样本数）个二元组，较score，最后得到AUC。间复杂度为$O(N*M)$.

###  hr hit ratio@K
1. in MF-BPR model , if test dataset inlcudes 1 positive sample and 999 negative sample , if the positive ranks in the top-K prediction , we say it hit the user, mean that the $hit ratio@K = 1$ 

### ndcg (Normalized Discounted Cumulative Gain)
1.  DCG
    1.  是一个衡量搜索引擎排序算法的指标
    2.  DCG的评测方法的两个前提
        1.  搜索结果中，相关度越高的结果，排在前面越好
        2.  在PI标注时，等级高的结果比等级低的结果好
    3.  搜索引擎一般采用PI(per item)方式进行评测，对每个排序的item进行逐条分。比如：在Google给出5个结果，我们对结果评级为[3,1,2,3,2]
    4.  $DCG_p = rel_1+\Sigma_{i=2}^p (rel_i)/(log_2^i)$
    5.  BPR中，neg test data比pos test data排名小的个数，可以当做$rel_i$
    6.  则计算3的为DCG = 3+(1+1.26+1.5+0.86) = 7.62
    7.  要在CG运算中中加入位置信息的计算。假设每个位置按照从小到大排序，它们价值依次递减，
2.  IDCG
    1. IDCG(ideal DCG) 理想的DCG。如：[3,3,2,2,1] IDCG = 3 + (3 + 1.26 +1 + 0.43)
    2. $rel_i$越大，排的越靠前，则DCG越高，越接近IDCG
3.  NDCG
    1.  NDCG = DCG / IDCG
    2.  在BPR中，如果hit中，那reli为1，如果hit不中，那么reli为0，再根据换地式则可推出代码中的公式