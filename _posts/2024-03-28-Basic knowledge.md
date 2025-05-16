---
title: Basic Knowledge
date: 2024-03-28 10:59:41 +0800
categories: [CS, Coding]
tags: [notes]    # TAG names should always be **lowercase**
---

## Brief Introduction

一些基础知识点（八股）

## C++关键字

### decltype

类型推导关键字，它能在编译阶段分析表达式的类型。主要功能是获取表达式的类型，而且不会对表达式进行求值。其语法格式如下：

``` c++
decltype(expression) var; // 用表达式的类型来声明变量
```

**应用场景**

1. lambda表达式

   对于一些难以手动书写的类型，像 lambda 表达式的类型，`decltype` 可以直接获取。

   ``` c++
   auto lambda = [](int x) { return x * 2; };
   decltype(lambda) anotherLambda = lambda;
   ```

2. 定义与表达式类型一致的变量

   ``` 
   int x = 42;
   decltype(x) y = 10; // y 的类型是 int
   ```

3. 泛型编程与模版元编程

   在模板编程中，当函数返回值的类型依赖于模板参数时，`decltype` 就能发挥很大作用。

   ``` c++
   template<typename T, typename U>
   auto add(T t, U u) -> decltype(t + u) {
       return t + u;
   }
   ```

   这里借助 `decltype` 推导出 `t + u` 的类型，以此作为函数的返回类型。

