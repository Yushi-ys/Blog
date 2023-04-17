上周，群里的一个朋友，跳槽某大厂，被问了一个问题

![38f9063299066a9014f5f1b372cbd159.png](https://s2.loli.net/2023/04/17/bAqjktNRcpJPwZx.png)

自己想起来好像是和什么银行家舍入法啥的相关，但是忘记了，所以记录一下这个

说这个问题之前，先说一下一个非常经典的问题，为什么0.1 + 0.2 不等于 0.3

因为在JS里，不管是整数还是小数，在做运算的时候，都是先从十进制 => 二进制，对二进制相加，然后把相加的结果再 => 十进制，这个过程就会出现精度的丢失，过程如下：
```js
先计算0.1的二进制
0.1 * 2 = 0.2       整数部分0
0.2 * 2 = 0.4       整数部分0
0.4 * 2 = 0.8       整数部分0
0.8 * 2 = 1.6       整数部分1
0.6 * 2 = 1.2       整数部分1
0.2 * 2 = 0.4       整数部分0

0.1的二进制 0.0001100110...

以此类推，0.2的二进制 0.00110011...

相加之后再转化成十进制就不是0.3了
```
说上面这些，就是想说，JS对数据的存储不是你们看到的样子！！！


用一句话来概括银行家舍入法就是 **四舍六入五成双**，具体规则如下：

1. 保留位数的后一位如果小于5，则舍去
2. 保留位数的后一位如果大于5，则进位
3. 保留位数的后一位如果是5
   + 且5后面仍有数，则无论奇偶都要进入
   + 若5后面不再有数，要根据尾数“5”的前一位决定: 如果是奇数则进入，如果是偶数则舍去

举个例子：
```js
(以保留两位小数为例)
5.214 ≈ 5.21（4小于5）
5.216 ≈ 为5.22（6大于5）
5.2254 ≈ 5.23（5后面有数，进入）
5.215 ≈ 5.22（5后面没数，前一位1是奇数，进入）
5.225 ≈ 5.22（5后面没数，前一位2是偶数，舍弃）
```
看看结果

![image.png](https://s2.loli.net/2023/04/17/S5Y6lp4xIVKE3LD.png)

可以看到，并没有按照我们的推测，5.215的保留两位变成了5.21

回到一开始讲的精度那一块，我们来看看这个5.215到底是什么

![image.png](https://s2.loli.net/2023/04/17/gwP4AZlGBkyqXfY.png)

可见，按照保留两位小数来算，他的保留数的后一位是4，直接舍去了，所以答案是5.21

解决方法：对Number的toFixed方法重写，如下：
```js
// 重新写入 toFixed 方法
export default Number.prototype.toFixed = function (n) {
    if (n > 20 || n < 0) {
        throw new RangeError('toFixed() digits argument must be between 0 and 20');
    }
    const number = this;
    if (isNaN(number) || number >= Math.pow(10, 21)) {
        return number.toString();
    }
 
    if (typeof (n) == 'undefined' || n == 0) {
        return (Math.round(number)).toString();
    }
    let result = number.toString();
    const arr = result.split('.');
    // 整数的情况
    if (arr.length < 2) {
        result += '.';
        for (let i = 0; i < n; i += 1) {
            result += '0';
        }
        return result;
    }
    const integer = arr[0];
    const decimal = arr[1];
    if (decimal.length == n) {
        return result;
    }
 
    if (decimal.length < n) {
        for (let i = 0; i < n - decimal.length; i += 1) {
            result += '0';
        }
        return result;
    }
    result = integer + '.' + decimal.substr(0, n);
    const last = decimal.substr(n, 1);
    // 四舍五入，转换为整数再处理，避免浮点数精度的损失
    if (parseInt(last, 10) >= 5) {
        const x = Math.pow(10, n);
        result = (Math.round((parseFloat(result) * x)) + 1) / x;
        result = result.toFixed(n);
    }
    return result;
}
```

[附一份toFixed源码地址](https://source.chromium.org/chromium/chromium/src/+/main:third_party/devtools-frontend/src/node_modules/core-js-pure/modules/es.number.to-fixed.js;l=42?q=toFixed&ss=chromium)