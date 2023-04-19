原题在这：
https://leetcode.cn/problems/count-number-of-homogenous-substrings/description/

直接贴代码

```js
const getNums = (n) => {
  return ((n + 1) * n) >> 1;
};
/**
 * @param {string} s
 * @return {number}
 */

var countHomogenous = function (s) {
  const MOD = 1000000007;
  let left = 0,
    right = 0,
    cnt = 0;
  const len = s.length;
  while (right < len) {
    const left_word = s[left];
    while (right < len && s[right] === left_word) {
      right++;
    }
    cnt = (cnt + getNums(right - left)) % MOD;
    left = right;
  }
  return cnt;
};
```

思路很简单，对于某个连续字串 aaaa 来说，同构字符串 a 出现 4 次，aa 出现 3 次，aaa 出现 2 次，aaaa 出现 1 次。可以看出对于长度为 n 的相同字母的连续字串，同构字符串的出现次数为 1 + 2 + ... + n, 即(n + 1) \* n / 2。我们从前往后遍历字符串的每一个字符，统计相同连续字母的子串长度，每次累加长度即可

但是提交后却发现

![LC-1759提交答案错误.jpg](https://s2.loli.net/2023/01/08/KdrusIMm5wEiz6n.jpg)

我锁定问题出在 getNum 函数，这里我习惯性的使用了位运算去求(n + 1) \* n 除以 2 的结果，打印了下长度，我发现形参 n 的长度为 100000，也就是 1e5，很显然，(1e5 + 1) \* 1e5 约等于 <font color="red">1e10</font>

然后我看到了这篇文章：https://www.jianshu.com/p/49979d935716

得到以下结论：在 JavaScript 内部，数值都是以 64 位浮点数的形式储存，<font color="red">但是做位运算的时候，是以 32 位带符号的整数进行运算的，并且返回值也是一个 32 位带符号的整数。</font>而 32 位整数数值范围从-2147483648 到 2147483647，很显然 1e10 不在这个范围，所以位运算就会出错...

(把那里换成 /2 就好了)