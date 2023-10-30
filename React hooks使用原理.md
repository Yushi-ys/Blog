代码

```js
import React from "react";
import ReactDOM from "react-dom";
import { Button } from "antd";
import { isEqual } from "lodash-es";
import "./styles.css";

let memoizedState = []; // hooks 存放在这个数组
let cursor = 0; // 当前 memoizedState 下标

function useState(initialValue) {
  memoizedState[cursor] = memoizedState[cursor] || initialValue;
  const currentCursor = cursor;
  function setState(newState) {
    console.log(currentCursor);
    memoizedState[currentCursor] = newState;
    render();
  }
  return [memoizedState[cursor++], setState]; // 返回当前 state，并把 cursor 加 1
}

function useEffect(callback, depArr) {
  const deps = memoizedState[cursor];
  const hasChangedDeps = !isEqual(deps, depArr);
  if (!depArr || hasChangedDeps) {
    callback();
    memoizedState[cursor] = depArr;
  }
  cursor++;
}

function App() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  const [count3, setCount3] = useState(0);

  useEffect(() => {
    console.log("不传依赖数组");
  });

  useEffect(() => {
    console.log("开始的时候执行一次");
  }, []);

  useEffect(() => {
    console.log("count1改变的时候执行");
  }, [count1]);

  useEffect(() => {
    console.log("count2或者3改变的时候执行");
  }, [count2, count3]);

  return (
    <div>
      <div>{count1}</div>
      <Button
        onClick={() => {
          setCount1(count1 + 1);
        }}
      >
        点击第一个
      </Button>
      <div>{count2}</div>
      <Button
        onClick={() => {
          setCount2(count2 + 1);
        }}
      >
        点击第二个
      </Button>
      <div>{count3}</div>
      <Button
        onClick={() => {
          setCount3(count3 + 1);
        }}
      >
        点击第三个
      </Button>
    </div>
  );
}

const rootElement = document.getElementById("root");

function render() {
  cursor = 0;
  ReactDOM.render(<App />, rootElement);
}
render();
```

代码关键在于：

初次渲染的时候，按照 useState，useEffect 的顺序，把 state，deps 等按顺序塞到 memoizedState 数组中。
更新的时候，按照顺序，从 memoizedState 中把上次记录的值拿出来。

一些常见的问题：

Q：为什么第二个参数是空数组，相当于 componentDidMount ？

A：因为依赖一直不变化，callback 不会二次执行。

Q：为什么只能在函数最外层调用 Hook？为什么不要在循环、条件判断或者子函数中调用。

A：memoizedState 数组是按 hook 定义的顺序来放置数据的，如果 hook 顺序变化，memoizedState 并不会感知到。(换句话说， hooks 每次 render 的时候，会对应到上次调用的顺序，如果放到条件语句里，如果某一次不执行，会打扰原先的顺序，使得后面的一个 hook 对应到当前的 hook。当然，循环里也一样，比如 for i 〈 5，这里的 i < 5 也是一个条件语句)

Q：自定义的 Hook 是如何影响使用它的函数组件的？

A：共享同一个 memoizedState，共享同一个顺序。

Q：“Capture Value” 特性是如何产生的？

A：先说一下什么是 Capture Value。React 的组件每次 Render 的内容都会形成一个快照并保留下来，因此当状态变更而 Rerender 时，就形成了 N 个 Render 状态，而每个 Render 状态都拥有自己固定不变的 Props 与 State

```js
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  return (
    <div className="App">
      <button onClick={() => setCount(count + 1)}>+1</button>
      {count}
      <button
        onClick={() =>
          setTimeout(() => {
            alert(count);
          }, 2000)
        }
      >
        展示
      </button>
    </div>
  );
}

export default App;
```

点击几次 +1 后，点击 show，此时再多次点击 +1，会发现 alert 出来的值是点击 show 之前的，而不是最新的，这就是 Capture Value，alert 拿到的只是那一次快照的值（可以用 ​useRef 去解决，就不多说了。可以认为 ​​ref​​ 在所有 Render 过程中保持着唯一引用，因此所有对 ​​ref​​ 的赋值或取值，拿到的都只有一个最终状态，而不会在每个 Render 间存在隔离）

Q: 每一次 ReRender 的时候，都是重新去执行函数组件了，对于之前已经执行过的函数组件，并不会做任何操作。
