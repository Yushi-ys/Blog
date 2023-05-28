上周五，快下班的时候，刷起了 LeetCode 中的新增专题(JavaScript)的题目，遇到了一题，第一次提交超时了，后来看评论区，我把数组的 concat 方法替换成了 push 方法，就过了

原题链接：[扁平化嵌套数组](https://leetcode.cn/problems/flatten-deeply-nested-array/description/)

超时代码

![image.png](https://s2.loli.net/2023/05/28/7e4H26LvODgXwux.png)

正确代码

![image.png](https://s2.loli.net/2023/05/28/XN6vagJcAPilem1.png)

差别之处就是 **res = res.concat(item) => res.push(...item)**

我们写个小测试来对比一下二者的速度

```js
let res = [];

// 使用 .concat 合并 15000 个拥有 5 个元素的数组
const demo = [1, 1, 1, 1, 1];
console.time();

for (let i = 0; i < 15000; i++) {
    res = res.concat(demo);
}

console.timeEnd();
// 平均下来时间大概是 630 ms

```

```js
let res = [];

// 使用 .push 合并 15000 个拥有 5 个元素的数组
const demo = [1, 1, 1, 1, 1];
console.time();

for (let i = 0; i < 15000; i++) {
    res.push(...demo);
}

console.timeEnd();

// 平均下来时间大概是 2.2 ms

```

可以看到 push 比 concat 在上述情况下，快了 <font color="red">300</font> 多倍，若更复杂的场景，恐怕差距就更大了

至于为什么，可以看两种 api 的实现原理 [concat](https://github.com/Yushi-ys/Front-End-Collect/blob/main/%E6%89%8B%E5%86%99%E9%A2%98/concat.js) [push](https://github.com/Yushi-ys/Front-End-Collect/blob/main/%E6%89%8B%E5%86%99%E9%A2%98/push.js)，push是在原数组上原地修改，而 concat 需要额外将第一个数组的元素复制给这个新数组，所以 push 会更快