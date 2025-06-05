---
title: Commonly used functions
date: 2024-03-28 10:05:37 +0800
categories: [CS, Coding]
tags: [notes, functions]    # TAG names should always be **lowercase**
---

## Brief Introduction

记录了一些刷题常用的函数，便于复习

## 排序类

### sort()

**头文件：**`#include <algorithm>`

**函数签名：**

1、`void sort (RandomAccessIterator first, RandomAccessIterator last);`

2、`void sort (RandomAccessIterator first, RandomAccessIterator last, **Compare comp**);`

这里的 `comp` 是一个二元比较函数，它规定了排序的顺序：

- 当 `comp(a, b)` 为 `true` 时，在排序后的结果里 `a` 会位于 `b` 之前。
- 从逻辑上来说，`comp` 定义了一种 “严格弱序” 关系，类似于 `<` 运算符的功能。

**使用方法：**

* 使用1的情况下，默认将`[first, last)`区间内的元素升序排列（**左闭右开区间**）
* 若使用自定义排序，则需使用2，根据指定排序规则`Compare comp`,将 `[first, last)`区间内的元素按照用户指定的顺序排列。

使用场景：由于在排序过程中涉及到元素*交换*等操作，所以sort函数仅支持**可随机访问**的容器，如数组， string、vector、deque等。

**示例：**

默认情况（升序排列）：

降序排列（适用于内置数据类型）：

``` c++
// 以int类型为例
#include <algorithm>
#include <vector>
#include <iostream>

using namespace std;

int main() {
	// 方式一、使用数组
	int a[5] = {0, 1, 2, 3, 4};
	sort(a, a + 5, greater<int>());	// 注意这里的 a + 5，因为右边是开区间
	
	for (int i = 0; i < 5; i++) {
		cout << a[i] << ' ';		// 输出排序后数组
	}
    cout << endl;	// 输出 4 3 2 1 0 

    // 方式二、使用 vector
    vector<int> arr = {9, 6, 3, 8, 5, 2, 7, 4, 1, 0};
    sort(arr.begin(), arr.end(), greater<int>()); 
    for (int i = 0; i < 10; i++) {
    	cout << arr[i] << ' ';	// 输出排序后数组 9 8 7 6 5 4 3 2 1 0 
	}
	
    return 0;
}
```

1. 在`cmp`处传入`greater<int>()`函数，**`greater<T>` 的定义**
   `greater<T>` 对 `operator()` 进行了重载，其返回值遵循以下规则：

   ```c++
   struct greater {
       bool operator()(const T& a, const T& b) const {
           return a > b;
       }
   };
   ```

   当 `a > b` 时，该函数返回 `true`。

   根据`sort`函数的排序方法，a会在b前面，即降序排列

自定义数据结构的排序：

``` c++
// 主要是实现相应的cmp函数
// 简单数据结构
bool cmp(int num1, int num2) {
    return num1 > num2;     // 可以简单理解为 > 降序排列;  <  升序排列
}

// 结构体
struct Student {    // 学生结构体
    string name;    // 学生姓名
    int grade;      // 学生分数
    Student();  // 无参数构造函数
    Student(string name, int grade) : name(name), grade(grade) {};  // 有参数构造函数
};
bool cmp(Student s1, Student s2) {  // 自定义排序
    if (s1.grade != s2.grade) {     // 如果学生成绩不相同
        return s1.grade > s2.grade; // 则按照成绩降序排列
    }
    return s1.name < s2.name;   // 否则按照姓名升序排列
}

// 或者使用匿名函数即lambda表达式
```



参考资料：

1. [CSDN_1](https://blog.csdn.net/VariatioZbw/article/details/125155432)
2. [CSDN_2](https://blog.csdn.net/qq_41575507/article/details/105936466)