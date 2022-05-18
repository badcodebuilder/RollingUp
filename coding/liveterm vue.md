# LiveTerm-vue

啊啊啊居然真的有人实现出来了，本来3月份买服务器准备建站的时候，想到了这个点子，就是做一个网页上的shell（原谅我分不清shell和terminal）模拟器，然后。。。摸了（恨不得给自己俩大耳刮子）。[LiveTerm](https://github.com/Cveinnt/LiveTerm)，在线Terminal模拟器，基于[Next.js](https://nextjs.org/)，完全没了解过。

立即试了一下，很有意思，可以自己加一些奇怪的命令，比如说：

```typescript
export const sudo = async (args?: string[]): Promise<string> => {
  // ...I'm sorry
  window.open('https://www.bilibili.com/video/BV1GJ411x7h7', '_blank');
  return `Permission denied, or you can pwn me =w=`;
};
```

不过出于生产环境的考虑，我还是想把这段代码打包一下，编程静态代码放上去，这样效率应该会好很多吧。然后`yarn build`发现没有我想象的代码呀，Issue里面也有人提出来想要静态，但是作者指出不能静态。呜呜qwq，怎么就不能呢？

那么，立志做full-stack的我应该练习一下怎么写前端了。既然LiveTerm用的React，那么我必然要用Vue了呀；既然不能打包，那我必然要能打包呀；既然……重复造轮子太坏了，应该Issue和PR的。在深入研究需求以及尝试LiveTerm之后，其实发现了一些问题：

1. 命令行不能自动折行，这一点是因为用了`<input>`导致的
2. 命令行不支持多行，一般超长的命令行是可以用`\`来换行的
3. tab功能没有zsh丰富（可能我也做不出来）
4. 该项目应该不是前后端分离的，至少应该所有命令全部交给API来处理才算前后分离吧（这又增加一堆工作量）

所以我准备复刻一个`LiveTerm-vue`作为自己的学习vue的作业，目前repo还没公开，想摸鱼的时候就来打一点代码
