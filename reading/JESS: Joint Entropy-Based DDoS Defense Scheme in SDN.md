# JESS: Joint Entropy-Based DDoS Defense Scheme in SDN

> Kübra Kalkan etc.
>
> IEEE Journal on Selected Areas in Communications CCF A, JCR Q1, 9.144

一看论文题目就知道是用的熵，目前主流的检测手段（机器学习，熵）之一。这里用的是联合熵。

文章的第一部分就明确指出，目前的外部检测手段可以分为基于统计的方案和基于机器学习的方案，优缺点不言而喻（在速度，准确度，实现难度上），不过后面说基于统计的方案缺少缓解方法，这一点有点不能理解，难道这两个不是分开的吗？也就是说检测干检测的事情，缓解干缓解的事情，两者通过一种协议来交互。难道说是因为检测模块得到的数据不一样，导致缓解模块也要对应，使得这样的通用协议不存在？

## JESS架构

JESS分为三个阶段：标定，预备，缓解（翻译可能不准确，但是从实际功能上差不多是这样）。当流量带宽超过标定的阈值的时候，就进入第二阶段，当在预备阶段中会生成一些相关的参数。第三阶段会防御和拦截。

> 说实话有点迷惑，说的也太模糊了？原文是这样的：
>
> When the traffic bandwidth exceeds the nominal threshold, the second phase called preparatory stage goes into action. In this phase, the DDoS attack is detected and relevant parameters are determined. Then the last phase denoted as active mitigation stage which defends the system during the attack goes online.
>
> 这一小段引入了很多概念，大约写了半页纸的概念后才给出详细的解释，读起来有点迷惑，建议跳过这一节前面的概述部分，直接看三个阶段，然后在回过头来看概述。（而且最后一句有语法问题吧）

### 标定阶段

这一节乍一看有点吓人，但是读懂了还是不算太难的。

1. Nominal Profile Generation

    说起来一大堆，实际上比较容易实现，大致是如下的流程（Pyhton写的伪代码，一定要看懂类型注释）

    ```python
    """
    class Feature:
        # features extracted from packet header
        pass
    """
    profiles: List[Dict[Tuple[Feature, Feature], int]] = [{} for _ in range(n*(n-1)//2)]

    while received_pkt_cnt < TARGET_CNT:
        pkt_header = receive()
        pkt_feature: List[Feature] = feature(pkt_header)
        assert len(pkt_feature) == n

        index = 0
        for i in range(n-1):
            for j in range(i+1, n):
                # if not exist, it should be created
                profiles[index][(pkt_feature[i], pkt_feature[j])] += 1
                index += 1

        received_pkt_cnt += 1
    ```

    获取报文头部的特征，然后选择k个特征无序组合成特征组，这样会有$C_{len}^k$个特征组，每个特征组是一个哈希表，键是特征组的实际值，值是特征组实际值的计数。当一个报文实例来的时候，计算所有的特征组实际值，然后添加到对应的哈希表中。

2. Nominal Profile of Order p

    更加简单，将1的操作重复多次，通过增加统计量来提高普适性

3. 交叉熵计算

    根据大数定理，用计数除以统计次数得到概率。然后计算各个特征组的交叉熵（哪是交叉熵啊，公式4就是普通的熵，交叉熵至少得有两个分布吧），然后归一化（搞不懂归一化公式7为啥$\alpha$要取整，次数本来就应该是整数吧）

### 准备阶段

如果上面那个阶段的过程搞懂了，这里就比较简单了。当带宽超过一定值的时候，启动记录，按照标定阶段的方法得到现在的各个特征组的交叉熵，和标定阶段的对应的交叉熵做差，取最大值（没有取绝对值也很奇怪），这个交叉熵差对应的特征组就应该是攻击缓解中重点关注的对象，文中称为*SuspiciousPair*

> 到这里也就能理解为什么说该种办法对于未了解的攻击手段也可以起到一定的检测和保护作用了，因为攻击基本是同一种方法，如果说在攻击中，攻击行为的熵（就是说上面的交叉熵）不怎么变化的话，就很可能被检测出来。

### 缓解阶段

1. 生成*SuspiciousPair*当前特征组的Profile（下简称为SC）
2. 将SC中的每个PAC与标定特征组Profile中的PAC相比，求得一个$\beta$值，作为得分。**注意**：这里SC的条目在SN中不一定存在，硬除就会除0错误，文中没说怎么处理。
3. 计算一个阈值，计算过程在本文中只是提到了一个*load shedding algorithm*，在文献[\[1\]](#ref1)中有提到，有空来补充一下（又是flag）。
4. 更新规则，得分超过阈值就丢弃，否则转发，并且是差分更新（指哪些规则更新了才发送给交换机）

## 实验过程

自行查看_(:3」∠)_，数据集是MAWILab提供的数据，实验环境是非常常见的1控制器1交换机3主机结构，攻击类型包括：

+ TCP SYN洪泛
+ DNS放大攻击
+ NTP攻击
+ 通用攻击（参数随机的攻击）
+ 混合攻击，多种形态的攻击

衡量标准也是很常见的误报漏报率这样

## 后续讨论

1. 每天要收集几次profile？有研究表明每天相同时间的profile是类似的
2. 阶段长度（即每次收集的报文数量）不能少也不能多，少了不准确，多了运算量大
3. 需要写成<abbr title="Virtual Network Function">VNF</abbr>来保障安全
4. 双11和DoS的区别，双11每个客户端发包数量比较少，因为是手动发包，DoS则不是
5. 第一第二阶段需要将所有报文头发送给控制器，开销比较大，如何控制

## 个人的一点看法

1. 真的是交叉熵吗？是我论文里面公式看错了还是网上对于交叉熵的解释不对，还是我对于网上的解释理解不对？反正我是没看出来公式4里面两个变量有什么区别
2. 机器学习真的不能做吗？本文的重点应该是在说如何选择合适的特征组吧，交给机器学习来解决应该问题不大吧，不过就是开销问题罢了
3. 符号太多啦，花体斜体都来了，花体一般表示集合呀，这样表示让人比较头晕，特别是像我这种符号恐惧症的人，不过后面的描述倒还清晰明了

## 参考文献

<div id="ref1">[1] S. Kasera, J. Pinheiro, C. Loader, M. Karaul, A. Hari, and T. LaPorta, “Fast and robust signaling overload control,” in Proc. 9th IEEE Int. Conf. Netw. Protocols (ICNP), Nov. 2001, pp. 323–331.</div>
