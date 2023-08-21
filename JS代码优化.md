#### 1. 慎用全局变量

为什么？

1. 全局变量定义在全局上下文，位处于作用域的顶端。每次查找这个变量的时候都需要从局部找到最顶端，消耗大量时间
2. 全局执行上下文一直存在于上下文执行栈，直到程序退出，**内存空间浪费**
3. 会产生局部污染

举例：

##### 全局变量：

![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230821210708.png)

##### 局部变量：

![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230821210837.png)

#### 2. 缓存全局变量

- 将使用中无法避免的全局变量缓存到局部，下面可以看到缓存过的比没有缓存过的性能要有所提高

网上的图
![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230821212811.png)

#### 3. 通过原型新增方法

尽量在原型对象上新增实例对象需要的方法，而不是在构造函数内部添加方法

![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230821215911.png)

#### 4. 避免属性访问方法使用

JavaScript 中的面向对象中

1. JS 不需要属性的访问方法，所有属性都是外部可见的
2. 属性访问方法只会增加一层重定义，没有访问的控制力

![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230821221230.png)

#### 5. for 循环优化

**这也是在算法题中经常出现的，缓存数组的 length**

![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230821221601.png)

![](https://yushi0703.oss-cn-nanjing.aliyuncs.com/Blog/20230821221706.png)
