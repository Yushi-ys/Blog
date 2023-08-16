### JSON 解析要比 object 快 (（这个是显然易见的，JSON 语法要比 object 简单多了，解析成本要低很多），Google 建议大于 10KB 的 JSON 可以尝试这种优化)

#### 看一下测试

![image.png](https://s2.loli.net/2023/08/16/RDwBUL8OMevph2t.png)

#### 代码如下：
```js
const N = 1e7 + 1;

console.time("Object解析的时间：");
for (let i = 0; i < N; i++) var obj = { foo: 42, bar: 1337 };
console.timeEnd("Object解析的时间：");

console.time("JSON.parse解析的时间：");
for (let i = 0; i < N; i++) var json = JSON.parse('{"foo":42,"bar":1337}');
console.timeEnd("JSON.parse解析的时间：");

```

可以看到在 N 次解析下，JSON.parse 比 Object 快很多，肉眼可见的明显

#### 场景：
前端较大的数据管理库可能会遇到（比如说很大的一个 redux 状态对象），其余场景没必要因为这点儿性能降低代码维护性