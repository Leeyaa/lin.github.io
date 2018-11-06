---
title: NFM阅读笔记
date: 2018-11-6 09:55:22
categories:
- Recommender System 
---


第一篇引入Bi层，将神经网络引入FM的论文，经典的推荐系统模型。发表在2017年SIGIR上。

<!-- more -->
1. Abstract
    - FM模型并不能表示非线性的模型关系与数据本身存在的内在联系所以需要加入神经网络来加入非线性联系 
    - 添加了深度神经网络之后，容易使模型变得难以训练，所以NFM加入了相较而言比较浅层的神经网络，使得模型在提升效率的同时容易训练
2. INTRODUCTIONS
    -  FM因为基于线性模型与两两交互，所以对于真实数据中的非线结构表达不够，对多个特征之间的复杂联系表达也不够。引入了高交互之后，也仅仅只是相对两两交互的FM改善了marginalimprovements，原因可能是线性的高阶相互作用模型。
    -  通过**Bi-Interaction** pooling的发明，第一次将FM引入神经网络模型中来。Bi-Interaction pooling层是一个新的概念也是本文的主题概念。
    -  **W&D中的Embedding和NFM中是一回事吗？**
    -  比官方的FM效果要好，比Wide&Deep模型和DeepCross模型参要少，层数要少，训练时间也就相对较短。
3. MODELING FEATURE INTERACTIONS
    1. FM模型简述
        - FM是一个很棒的泛化模型
        - 为了解决FM的表达问题，作者通过将双边交互特征的隐藏量空间进行非线性转换，来使FM模型获得非线性能力，与此时可以获得高阶交互信息
    2. **DNN**本篇文章重点创新
        1.  基于神经网络的基本模型是:将嵌入的特征向量串联后，上面堆叠多层神经网络，来学习特征之间的关联。期望多层网可以隐式的学习到任意顺序的组合特征。但是我们发现，仅仅过特征向量之间的串联这种结构只能得出很少的特征关联信息要学习到有意义的信息，我们只能通过接下来的深层神经网络进行学习。但是这种结构训练起来又会遇到很多问题，比如梯消失/爆炸，过拟合，退化等等。
        2.  使用了FM embedding特征之后，为什么从图中可以看出WD的退化现象被很好的处理了，而DC却还是存在着严重的过拟现象呢？
            - **什么是RMSE？**
                - MSE:均方误差$\frac{1}{m}\Sigma_{i=1}^(y_i-y_i^1)^2$
                - RMSE:均方根误差$\sqrt{\frac{1}{m}\Sigma{i=1}^m(y_i-y_i^1)^2}$
            - **什么是degradation？**
                - degradation,亦即随着网络深度的增加，准确饱和并且迅速减少。
            - **从图中应该看不出退化现象被处理以及只能看到过合现象还仍然严重**
        3.  Epoch ，Batchsize，Iteration
            - **Epoch**:一个epoch是指将所有训练样本都训练一遍
            - **Iteration**:完成一个Epoch需要进行的迭代次数
            - **Batchsize**:是指在一次迭代中样本的个数
            - $Epotch = iteration*Batchsize$
4. NFM MODEL
    
    本节首先介绍了NFM模型，然后讨论了模型的学习步骤，最后介绍怎么使用神经网络中的实用技巧比如dropout、batchnormalization来改善NFM。
    1. **NFM模型**中，值为0的特征代表不存在。
        - **Embedding Layer**
            - 考虑到实值特征，将实值乘了Embedding之后的隐特向量
            - 暂时猜想，Embedding层是由FM方法来做的。
            - FNN中，DNN的输入就是FM学习到的隐藏向量。所以NFM中，需要FM先学习到隐藏特征向量，才能对DNN进行练。
            - **该层经试验，不提高模型性能，但可以提高模型收速度**
        - **Bi-Interaction Pooling**
            - 输入为FM的隐特征向量，输出出来一个向量
            - 推到过程同FM推到过程
            - 时间复杂度为$O(kN_x)$,按照（4）式右边的平方式来。
        - **Hidden layer**
            -  隐藏层为全连接
        - **prediction score**
            - 公式
        1. NFM generalizes FM
            - 删除隐藏层，并将预测函数向量h变成常数向量（1，1...，1）便可化为FM模型
        2. Relation to W&D and DeepCross
            -  与一些已有的深度网络模型不同点在于，使用Bi-Interaction pooling层，这层不仅仅是对于特征量的串联连接，而是将两两特征之间的联系在低阶进行合，使得后面的深度隐藏层更容易训练，并且学的更加用的高阶特征联系。
        3. Time complexity analysis
            - 因为隐藏层为全连接层，所以每层的时间复杂度为$(d_{l-1}d_l)$,第一层的$d_0=k$所以，整个NFM模型时间复杂度为BI层时间复杂度加上隐藏层时间复杂度：$(kN_x+\Sigma_{l=1}^Ld_{l-1}dl)$
    2. **Learning**
        1. 本文使用了mini-batch adagrad（自适应梯度下降）作优化器，而不是vanilla SGD. 主要优势是学习率可以自应，不用再进行调参，并且相对于vanilla SGD有更高的学效率
        2. **Bi-interaction pooling层的求导还没解决**
        3. **Dropout**是一种防止过拟合的正则化技术
            - 在Bi层和隐藏层都添加了dropout，来防止从互相适中学习到高阶特征交互与过拟合现象。 
        4. **Batch normalization**
            1. 为什么要做BN？
                - convariance shift现象，该现象指，测试集与练集数据的数据分布不一致现象，导致激活函（如，tanh）之后，数据之间差距不大。这导致了练变慢等现象。
            2. 怎么做？
                - 将每一个batch中输入层的输入标准化为一个高分布的零均值单位方差
            3. 公式解释
                - 均值：所有向量的平均值
                - 方差：方差用来计算每一个变量与总体均数之间差异 ，统计学采用平均离均差平方和来描述变量的异程度，$\sigma^2$为总体方差.
                - $\gamma,\beta$是可训练的向量，可以抵消一normalization的操作，因为一些normalizaiton没有作用的。
    3. **EXPERIMENTS**
        1.  几个问题
            - **RQ1**:Bi-interaction层是不是真的可以抓到特之间的两两关联？BN和dropout是怎么应用在BI层上的？
            - **RQ2**:NFM中的隐藏层是不是可以抓住更高阶的特关联并且提高FM的表达性？
            - **RQ3**:NFM模型的性能对比与高阶FM模型和最好的度学习模型W&D和DeepCross怎样？
        2.  Experimental Settings 
            1.  Datasets
                - frappe
                - MovieLens
            2. Evaluation protocal 
                - 数据集被分为三份，training、validationtest  validation用来调参，和证明前述问题RQ1RQ2。
                - 使用RMSE来估计模型性能
            3. Baselines
                - LibFM、HOFM、W&D、DeepCross
            4. Parameter Settings
                1. 本模型使用的参数：平法差损失函数，学习率dropout率，batchsize：128（frappe）409（movielens），没有特殊说明Embedding size64.
        3. Study of Bi layer（RQ1）
            1. dropout和L2一样，都可以防止过拟合，而且dropou性能要好一些
            2. BN添加之后，RMSE性能得到提升，并且可以增快模收敛速度，增加模型 的泛化能力。但是，BN和dropout一起使用，会导致训练过程波动变大。
        4. Impact of Hidden Layers（RQ2）
            1. Figure6证明，非线性激活函数是必须的，而且加入藏层与非线性函数，确实会提高模型性能。
            2. table2和替换Bi为串联的实验证明，Bi层已经编码充足的双边特征联系，所以，一个简单的一层非线性神网络已经可以充分抓住高阶特征之间的关联。这个证明可以使我们大大减轻了深度神经网络训练负担.
            3. Figure1& Figure7证明，第一，使用FM 作Embedding层，大大加快了收敛速度。第二，Bi层提供了模型更好的鲁棒性和易于训练，易于优化的性能。
        5. Performance Comparison（RQ3）
            1. NFM对比其他深度模型，拥有更好的性能。
            2. 深度的神经网络不一定是性能最好的，原因可能是因最优化的困难，以及模型overfitting的原因。
5. Conclusion
    - 一篇立意于将深度模型用来处理稀疏矩阵的文章，实验过程中现，深度模型不一定是性能最好的，合适的构件，才是提高性能的选。