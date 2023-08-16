### 在 React 中，我们常用 && 来达到 Vue 中 v-if 的作用

#### 例如

```js
const flag = true;

flag && <div>Hello JS</div>;
```

如果条件成立(flag 为真)，就渲染 && 后面的 dom 元素，这段代码中 div 标签的展示取决于 flag

但是在 React 中，某些情况会出现问题，如下：

```tsx
import { useState } from "react";
import "./styles.css";

const App: React.FC = () => {
  const [num, setNum] = useState(0);

  return (
    <div>
      <button onClick={() => setNum(num + 1)}>+1</button>
      {num && <h1>Hello JS!</h1>}
      <p>{num}</p>
    </div>
  );
};

export default App;
```

#### 逻辑很简单，当 num 不为 0 的时候，渲染 Hello JS

![image.png](https://s2.loli.net/2023/08/16/LR3EYu7VxZlj2eQ.png)
![image.png](https://s2.loli.net/2023/08/16/QZl9oIwj2bUtPyh.png)

<b>出现了一个 0？？？？？</b>

### && 的本质

对于 && 在 MDN 上的解释是:
一般来说，当从左到右求值时，该操作符返回第一个假值操作数的值；如果它们都是真值，则返回最后一个操作数的值

其本质也就是从左到右对整个操作进行求值，遇到第一个假值就会立即返回，如果所有的操作都是真值，则返回最后一个操作值

同时我们要知道转换成 false 的有以下内容：

false/null/NaN/0/空字符串/undefined

所以这个时候，问题来了，虽然会进行隐式类型转换，但是 && 运算符会保留所有非布尔值并原样返回它们

```js
"" && 1; // ''
0 && 1; // 0
NaN && 1; // NaN
```

所以上述代码同理，如果是 NaN，则会把 NaN 也渲染

### 如何解决

#### 1. 使用 !!

!! 可以强制把之转化为 boolean 类型

```js
!!arr.length && <Component arr={arr} />;
```

#### 2. 修改判断条件，不依赖隐式类型转换

```js
arr.length >= 1 && <Component arr={arr} />;
```

#### 3. 使用三元表达式，修改返回结果

```js
arr.length ? <Component arr={arr} /> : null;
```

#### 4. 使用判断条件

```js
if (arr.length) return <Component arr={arr} />;
```
