# SAFETY: Early Detection and Mitigation of TCP SYN Flood Utilizing Entropy in SDN

> Prashant Kumar etc.
>
> IEEE Transactions on Network and Service Management: CCF C, JCR Q2, 4.195(过于离谱，比之前那个高)

论文还没看，但是在github上找到了[相关代码](https://github.com/Danyson/Detection-and-Mitigation-of-SYN-Flood-Attack-against-Controller-in-SDN)，本着学习[RYU](https://github.com/faucetsdn/ryu)控制器怎么写的目的，先来看看代码写的怎么样吧。

## 代码

四个文件，两个代码两个说明，除没啥用的`README.md`，也就一个文档。从标题和文件名称就可以看出来这篇用的是信息熵。打开说明文件浏览了一下，emm，难道github不支持公式吗（写一个试一下 $e^{i\theta}=i\sin\theta+\cos\theta$）？再不济也可以LaTeX转MathML呀，不过一个CCF C也就不要强求了（但是国外看CCF吗？）

好了不bb看文档。有点奇怪，一方面只是对信息熵进行了简单的描述，另一方面直接计算字节值（原文是*byte value*）的信息熵？至少得是一个数值吧。emm算了，反正论文还没看，也不知道搞的什么玩意儿。一些信息熵的论文会用报文的目的地址这些出现的频率来作为计算熵的概率。

来看看代码

文件`DetectionAndMitigation.py`，唉只能说人家论文发表了那就是大爷。问题好多：

1. 代码里面缩进都不对怎么跑通的？八成是Tab空格混用
2. 不遵守PEP，函数参数都不标注类型，不过Ryu基本也没标注，极其烦人，并且像`ofp_event.EventOFPPacketIn`这样的变量都是动态生成的，我一脸问号，IDE都没法识别啊（code/pycharm都不行）

不过可以了解到一些比较有用的东西，比如说：

1. Ryu自带了一个hub，看上去像是线程管理器。查了一下是eventlet协程
2. Ryu就是写不同事件的触发器，和nonebot还挺像的嗷

另一个仔细一看是对上一个代码的优化，格式改的能看了，然后最后的一个大for循环好像没缩进，或者就是这样有意为之的。至少github上面没看出来有什么缩进，真的是不会写码吗？
