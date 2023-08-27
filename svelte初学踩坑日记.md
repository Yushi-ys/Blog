如图，子组件：

![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230827204345.png)

<font color="red">Svelte</font> 使用 <font color="red">export</font> 关键字将变量声明标记为属性

<font color="red">export</font> 并不是传统 <font color="red">ES6</font> 的那个导出，是一种语法糖写法，注意只要 <font color="red">export let</font> 是声明属性，<font color="red">export const export function export class</font> 这些写法为组件的只读属性，不会接受外面的传值

所以 如果设计组件传值，类似上面，**子组件不可以使用 export const**