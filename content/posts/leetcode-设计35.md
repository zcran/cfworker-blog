---
title: "leetcode-设计35"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 三合一

三合一。描述如何只用一个数组来实现三个栈。

你应该实现push(stackNum, value)、pop(stackNum)、isEmpty(stackNum)、peek(stackNum)方法。stackNum表示栈下标，value表示压入的值。

构造函数会传入一个stackSize参数，代表每个栈的大小。


```
/// 三合一栈：使用单个数组实现三个固定大小的栈
///
/// 存储布局：三个栈交错存储，每个栈的元素间隔为 3
/// 栈 0: 索引 0, 3, 6, 9, ...
/// 栈 1: 索引 1, 4, 7, 10, ...
/// 栈 2: 索引 2, 5, 8, 11, ...
///
/// 时间复杂度: 所有操作 O(1)
/// 空间复杂度: O(3 * stackSize)
struct TripleInOne {
    data: Vec<i32>,      // 底层存储数组
    tops: [usize; 3],    // 每个栈的"下一个可用位置"索引
    capacity: usize,     // 每个栈的最大容量
}

impl TripleInOne {
    /// 初始化三个栈，每个栈大小为 stack_size
    fn new(stack_size: i32) -> Self {
        let cap = stack_size as usize;
        TripleInOne {
            data: vec![0; 3 * cap],
            tops: [0, 1, 2], // 初始时每个栈的起始位置
            capacity: cap,
        }
    }

    /// 向指定栈压入元素
    fn push(&mut self, stack_num: i32, value: i32) {
        let idx = stack_num as usize;
        if self.tops[idx] < 3 * self.capacity {
            self.data[self.tops[idx]] = value;
            self.tops[idx] += 3;
        }
        // 栈满时静默忽略（符合题目要求）
    }

    /// 从指定栈弹出元素，空栈返回 -1
    fn pop(&mut self, stack_num: i32) -> i32 {
        let idx = stack_num as usize;
        if self.tops[idx] < 3 {
            return -1; // 栈空
        }
        self.tops[idx] -= 3;
        self.data[self.tops[idx]]
    }

    /// 查看指定栈的栈顶元素，空栈返回 -1
    fn peek(&self, stack_num: i32) -> i32 {
        let idx = stack_num as usize;
        if self.tops[idx] < 3 {
            return -1;
        }
        self.data[self.tops[idx] - 3]
    }

    /// 检查指定栈是否为空
    fn is_empty(&self, stack_num: i32) -> bool {
        let idx = stack_num as usize;
        self.tops[idx] < 3
    }
}
```
