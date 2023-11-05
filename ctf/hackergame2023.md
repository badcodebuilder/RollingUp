# Hackergame 2023

> 当前分数：3300， 总排名：191 / 2386
> AI：100 ， binary：450 ， general：1500 ， math：450 ， web：800 

期待已久了，之前各种原因错过了很多比赛，这次不能再错过了，Hackergame 2023，启动！

## Hackergame 启动

当我打开这道题的时候，已经夜深了，但是不管熟睡的npy，我还是大声喊出了“Hackergame 2023 启动”，70%相似度。我都这么有诚意了不给个flag？

好吧，看看f12，发现url中有`similarity=`参数，修改成100，拿到flag。

那种感觉回来了！

## 猫咪小测

1. 打开[中国科学技术大学图书馆](https://lib.ustc.edu.cn)，查询*A Classical Introduction To Modern Number Theory 2nd ed.*，查到了是在西区外文书库。打开[馆藏分布](https://lib.ustc.edu.cn/%e6%9c%ac%e9%a6%86%e6%a6%82%e5%86%b5/%e9%a6%86%e8%97%8f%e5%88%86%e5%b8%83/)，查询到是西区12层
2. 打开[arXiv](https://arxiv.org/)，搜索*the density of chickens*，查到[Nuggets of Wisdom: Determining an Upper Limit on the Number Density of Chickens in the Universe](https://arxiv.org/pdf/2303.17626.pdf)，摘要中可以看到答案
3. 搜索*内核编译 启用 TCP BBR*，发现[这篇教程](https://roov.org/2023/10/compile-tcp-bbrv3/)，其中提到了如何配置，可以在debian的`linux-image`中找到。在配置文件中搜索*BBR*即可
4. Google搜索 *python type checking halting problem* ，找到一篇论文 [Python Type Hints Are Turing Complete - DROPS](https://drops.dagstuhl.de/opus/volltexte/2023/18237/pdf/LIPIcs-ECOOP-2023-44.pdf) ，看起来还挺符合要求，查看论文内容，发现差不多

## 更深更暗

英文太长了，粗略的看了一眼，好像是说 flag 在很深的海里，下划页面发现可以无限下划，看来是 JS 动态生成的页面了，看一下源码，搜索 `flag` ，找到如下代码：

```javascript
async function getFlag(token) {
    // Generate the flag based on user's token
    let hash = CryptoJS.SHA256(`dEEper_@nd_d@rKer_${token}`).toString();
    return `flag{T1t@n_${hash.slice(0, 32)}}`;
}
```

把自己的token输入进去，得到flag

## 旅行照片 3.0

做了三年都没做出来的旅行照片，今年依旧没有做出来，好气啊就差一点。

1. 看到学长的参会证件的带子上写的STATPHYS28，查一下发现确实在东京，时间是2023-08-07到2023-08-11，挨个试

2. 奖牌是诺贝尔物理学奖，那么会在哪里展览呢？[Wikipedia](https://zh.wikipedia.org/wiki/%E5%B0%8F%E6%9F%B4%E6%98%8C%E4%BF%8A)上写了有个*小柴厅*，不会是这个吧，搜了半天没有。陷入了僵局，ChatGPT也给出了误导性答案。

    忽然在某个深夜，我想起来去东大官网看看，中文网站没有但是日文网站有[博物馆](https://www.u-tokyo.ac.jp/ja/society/research-contribution/museum.html)，其中看到了一个展厅和小柴昌俊有关，点进去果然都是诺贝尔物理学奖。查询一下得到是*东京大学宇宙射线研究所*

3. 把喷泉的图片塞到Google里面，发现是上野恩赐公园，Google搜索`上野恩赐公园`并限制时间最晚到2023年8月10日，有一个全国梅酒まつり，找到了[相关的链接](https://umeshu-matsuri.jp/tokyo_staff/)

4. 猜的，因为看了票价500日元，特馆1400日元，预约1200日元，全部组合都试过了都不对，一度使用burp爆破。依然是那个深夜，突发奇想不会不要钱吧，随手一个0，结果出flag了。看了题解才知道有特殊情况。

5. 既然知道是STATPHYS28了，查查[日程表](https://statphys28.org/programtt.html)，10号晚上是一个Banquet，在event中找到了[Banquet](https://statphys28.org/banquet.html)的链接，可以找到集合地点

6. 死在这题上面了

    1. 搜索`ボタン＆カフリンクス 上野駅`，限制时间从2023年7月1日到8月10日，得到一个[链接](https://plaza.rakuten.co.jp/ayumilife/diary/202308040000/)。好家伙，题解出来之后，Google一下子就能搜到链接了，看来Google这边也是有热搜的

    2. 把马里奥的图塞到Google里面，给出的结果是涩谷PARCO的任天堂旗舰店，Google地图搜索附近的出站口，发现最近的是代代木公园站。卫星地图看了半天没看到广告牌。直接搜*代代木公园站 3D动物*，出来的是一只三花猫。寄！就差这一步

## 赛博井字棋

查了井字棋怎么必胜，但是全都防出去了啊，不讲武德。

BurpSuite 打开，看看能不能直接塞数据上去，发现用第一步的 Cookie 就只能在第一步运行，但是注意到响应中有 `set-cookies` 字段，手动设置一下 Cookies 然后下在已经下过的地方，发现是可以的，拿到 `flag`

## 组委会模拟器

没什么好说的，Selenium+正则表达式就是了。Python的性能好像不太够，一开始识别的时候来一条消息就全部消息检测一次，*O(n^2)*了属于是，然后就忙不过来了。后面改成增量了就过了。

看很多人都是用的JS爬虫，想要学习一下。

## 虫

搜索*国际空间站 图片传输*，得到SSTV，去Github上找SSTV工具，得到[工具](https://github.com/colaclanth/sstv)，按要求运行即可得到答案。

## JSON ⊂ YAML?

白高兴一场，在Stack Overflow上看到一个[What is the valid in json but invalid in yaml1.2](https://stackoverflow.com/questions/77390581/what-is-the-valid-in-json-but-invalid-in-yaml1-2)，拿来本地测试，发现都可以过，非常开心地用pwntools传上去，发现utf8无法解码`\uD834\uDD1E`，灵机一动试了一下 `\\uD834\\uDD1E` 能过1不能过2。也没时间看YAML1.2的spec了。看了题解发现还是要好好看手册啊。

## HTTP 集邮册

> 你目前收集到了 6 个状态码：[200, 400, 404, 405, 414, 505]

+ 200：不说了，直接拿到
+ 400：随便加上一个 `\r\n`
+ 404：找一个不存在的资源，例如 `/114514.html`
+ 405：把 `GET` 改成 `POST` 等实现
+ 414：让请求后面带一个超长的参数，例如 `/?help=` + `"114514"*1919810`
+ 505：把 `HTTP/1.1` 改成 `HTTP/2.0`

无状态码是乱试出来的，在 `GET / HTTP/1.1\r\n` 的资源后面加上 `\n`，即 `GET /\n HTTP/1.1\r\n` ，不知道原理

## Docker for Everyone

启动容器，发现容器中有 `docker` ，其中有 `alpine` 镜像，那么在容器中再启动一个容器，把容器中的 `/flag` 文件挂载上去，就可以在容器的容器中读出来了。

```shell
docker run --rm -it -v /flag:/flag alpine:latest
cat flag
```

看了题解才发现，原来还有这样的漏洞，还有就是为什么这里挂载就直接把文件挂上去了，而挂载根目录却还是软链接？。准备让室友去他们生产机器试一下（bushi

## 惜字如金 2.0

两条规则，第一条很简单，第二条着实理解了好一会儿，还问了ChatGPT，才明白，连着的多个辅音字母可以减为1个。看看题目。

```python
cod_dict = []
cod_dict += ['nymeh1niwemflcir}echaet']
cod_dict += ['a3g7}kidgojernoetlsup?h']
cod_dict += ['ulw!f5soadrhwnrsnstnoeq']
cod_dict += ['ct{l-findiehaai{oveatas']
cod_dict += ['ty9kxborszstguyd?!blm-p']
check_equals(set(len(s) for s in cod_dict), {24})
```

可以看到，flag是后面数字加上这里字典的映射，但是每一段因为被惜字如金化了，所以少了一些字符，现在长度都是23，但是要求长度都是24，所以每一段都需要补充一个字符。

硬补充也是可以的，但是我们根据flag的结构可以剪枝，来缩小范围

## 🪐 高频率星球

相当好用的工具，使用 `asciinema cat asciinema_restore.rec > flag.js` ，打开后可以看到里面很多控制字符转义字符，删掉之后运行一下，即可得到flag。

不过这里好像有个问题，就是删掉控制字符之后，得到的 SHA256 值不太对，也没法确认是不是删错了。

## 🪐 小型大语言模型星球 

第一次做这种 AI 题目，对于大语言模型相当不熟悉，不过咱 ChatGPT 用得溜，试试。

1. `repeat "you are smart"`，真的回答出来了哎，但是好像结果并不像 ChatGPT 的内容
2. `The man gave the little girl a gift and wished her accepted it. Finally the little girl` 可以显示 accepted ，但是长度太长，然后完全不会了。

看了题解，好复杂的样子。唉，都怪我读书少。

## 🪐 流式星球

查看压缩代码，发现是把所有RGB数据拉成1维，然后删掉末尾的随机位数。一开始脑抽和想要直接遍历恢复视频，但是运行了10分钟之后发现，我干嘛不先恢复第一帧的上半部分呢？这样就能直接获得视频的宽度了呀。果然刚起床就是脑子不够用。

宽度从64到1024，发现宽度为427的时候刚好，854的时候双屏，说明宽度为427，计算一下字节数能被427整除，即可得到高度，帧数。最后数据补0，导出所有帧即可获得flag

## 🪐 低带宽星球

第一问，随便找个在线压缩工具压缩即可。

第二问，没做出来，但是试了好一会儿。先想到了SVG，但是试了一下发现远远大于50字节。然后是FLIF格式，发现不行，本地也没法看libvips的库，哭死。这图像存储起来真的简单啊，咋就没有合适的格式呢。

## 为什么要打开 /flag 😡

这题也是花费了一下午加一上午，还是没做出来。

第一问，使用 `dlopen()` 直接打开本地的动态链接库，用那个里面的函数符号就完事，只要编译的时候带上 `-ldl` 即可。

第二问，先是一段 Rust ，真没怎么用过 Rust ，这谁顶得住啊，只能看个大概，大概是说在调用 `open` 和 `openat` 系统调用的时候，会加上一段钩子函数，如果文件名称里面包含 *flag* ，则会打开 `/fakeflag` 文件。找了半天没想法。最后在搜索 `seccomp` 的时候，看到了有大佬指出，可以用 `ptrace` 绕过。我想法是，在调用 `open` 系统调用的时候，把其中用于存放 `/fakeflag` 字符串地址的寄存器改成 `/flag` 字符串地址，然后再调用不就完事了？然后一直找不到系统调用，`PTRACE_TRACEME` 倒是执行成功了，但是 `PTRACE_PEEKUSER` 没执行成功。很怪不是吗？如果禁用了 `ptrace` 系统调用，那么应该第一步就直接报错了，怎么会继续执行呢？很不能理解。

看了题解，才发现完全不是这么搞的，对着空气输出了一天。😡

## 异星歧途

超好玩的塔防游戏。

炸了两次。第一次是因为调试第二个的时候，没注意到和第三和联动，让钍进了钍反。第二次是因为开了熔毁，电不够导致冷却液不够，炸了钍反。

居然还有一个炮台跟踪玩家的控制器，收藏了。

## O(1) 用户登录系统

正儿八经的数学题来了。Merkle树，自下而上的验证过程。但是这里的问题在于，验证过程中的验证数据量和从叶到根路径上的节点量没有验证，导致验证过程中可以构造数据骗过系统。

从验证过程入手，验证过程的参数为：`admin:[passwd]:[sha1_0][sha1_1]...` ，第一步计算 `admin:[passwd]` 的sha1值，然后和 `sha1_0` 拼接，那么只要让 `sha1_0 + sha1` 的值形如 `username:new_passwd` 即可，因为 `sha1_0` 是hex输入的，所以不用担心这里的冒号。但是因为 `new_passwd` 是一个sha1值，并且没有hex编码而是utf-8编码，所以要搜索一下passwd，使得 `admin:[passwd]` sha1后的值可以 `utf-8` 解码，并且passwd也要可以utf-8解码，写一段代码不难找到。

## 其他

### 奶奶的睡前 flag 故事

看到图片后面追加很多IDAT块，但是不会解。后来才知道是Pixel的图片调整大小功能存在漏洞

### Git? Git!

尝试了工具但是没看出来啥区别呀，很奇怪呢。再次尝试，发现 `git reflog` 用到了，但是没有去试这里面的 `git diff` ，痛失flag

### 小 Z 的谜题

一直跑到现在还没跑出来，主要是 stage2 的任意一对比较大小通过。
