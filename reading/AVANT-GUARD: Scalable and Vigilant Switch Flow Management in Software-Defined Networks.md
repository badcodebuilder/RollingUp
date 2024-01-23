# AVANT-GUARD: Scalable and Vigilant Switch Flow Management in Software-Defined Networks

> Seungwon Shin etc.
>
> Proceedings of the 2013 ACM SIGSAC conference on Computer & Communications Security.(CCS，什么级别懂的都懂)

论文不要紧，要紧的其实是 `AVANT-GUARD` 这个名词，在很多论文里面用来作为相关研究以及 baseline ，因此这篇论文怎么能放过呢？目前读到了系统设计结束部分，赶紧记录一下这篇大几百引用的高质量论文吧。

本文提出的 `AVANT-GUARD` 是一个在数据层的改进，这一点就和很多仅靠现有 OpenFlow 标准提供的功能来做控制层的检测应用不同，说白了就是要修改 OpenvSwitch 的代码，工作量可想而知。然后就是本文提出的两个模块：连接迁移和驱动触发器。

## 连接迁移

这里参考了 SYN Proxy 的设计，在数据层开启 TCP 连接的代理，数据层先测试连接是否为恶意的，然后上报控制层，接着连接目标，再上报控制层，最后将源和目的地址连起来，完成连接。在此过程中收集相关数据，并作为攻击检测的相关数据。

## 驱动触发器

说白了就是一个条件触发，让数据层可以根据当前统计数据（例如端口发送的速率、报文内容等），触发相关的预设操作（例如回调函数、添加流表项等），或者上报控制器。有点好奇这里为什么不用流表的匹配项，因为报文相关的内容应该是可以用 `OFPMatch` 来解决的，再仔细一想这里其实还有统计量、当前规则启用情况，可能 BPF(Berkely Packet Filter) 都不太够，需要自己实现一些内容，所以单独抽出来了。

实验设计以及后续还没看，下次继续。