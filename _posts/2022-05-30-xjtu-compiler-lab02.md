---
title: "编译原理实验 02 | Cool 语言堆栈机"
date: 2022-05-30 19:31:54 +0800
tags: ["labs"]
---

## 1. 操作说明

| 命令  |              含义              |
| :---: | :----------------------------: |
| `int` |       将整数数添加到栈中       |
|  `+`  |          栈中压入 `+`          |
|  `s`  |          栈中压入 `s`          |
|  `e`  | 计算栈顶表达式的值（详见下文） |
|  `d`  |          打印栈中内容          |
|  `x`  |              退出              |

`e` 命令的作用：

1. 如果 `+` 在栈顶，那么 `+` 出栈，栈顶两整数弹出并相加，将结果压入栈
2. 如果 `s` 在栈顶，那么 `s` 出栈，下面两个选项在栈内交换

## 2. 栈的实现

### 2.1 内部成员变量

`cool` 语言是一种面向对象的语言，所有函数和值都是以对象的形式存在，栈的实现也要以对象的形式进行。由于不能直接创建数组，我们的栈使用嵌套的方式进行，栈内有 3 个变量：

```cpp
class Stack {

  top  : String;
  rest : Stack;
  size : Int <- 0;

};
```

- `top`：栈顶元素
- `rest`：栈中剩余元素，是当前 `Stack` 的子 `Stack`
- `size`：当前栈的深度，1 为最底层（打印输出时会减 1，即从第 0 层往上爬）。同时利用该数判断栈是否为空

### 2.2 初始化函数

`Stack` 类的核心就是它的初始化函数，在该函数中我们可以看到，栈是在 `rest` 的嵌套中实现的。

```cpp
  init(item : String, stack : Stack) : Stack {
    {
      top  <- item;
      rest <- stack;
      size <- stack.size() + 1;
      self;
    }
  };
```

### 2.3 入栈操作

入栈操作借助 `init` 实现：

```cpp
  push(item : String) : Stack {
    (new Stack).init(item, self)
  };

```

因为在 `Cool` 语言中不能直接对 `self` 赋值，我们只能通过下面的方式进行入栈操作：

```cpp
stack <- stack.push("<item>")
```

#### 2.4 出栈操作

出栈操作只需让当前的栈变为 `rest` 即可：

```cpp
  pop() : Stack {
    if self.size() = 0 then self
    else {
      top  <- rest.top();
      rest <- rest.remain();
      size <- size - 1;
      self;
    } fi
  };
```
