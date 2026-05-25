---
title: "leetcode-队列1"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 用队列实现栈

请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通栈的全部四种操作（push、top、pop 和 empty）。

实现 MyStack 类：

void push(int x) 将元素 x 压入栈顶。
int pop() 移除并返回栈顶元素。
int top() 返回栈顶元素。
boolean empty() 如果栈是空的，返回 true ；否则，返回 false 。


```
use std::collections::VecDeque;

pub struct MyStack {
    main: VecDeque<i32>,   // 主队列，队首是栈顶
    tmp:  VecDeque<i32>,   // 辅助队列，仅用于 push 中转
}

impl MyStack {
    pub fn new() -> Self {
        Self {
            main: VecDeque::new(),
            tmp:  VecDeque::new(),
        }
    }

    /// 将元素压入栈顶。时间复杂度 O(n)
    pub fn push(&mut self, x: i32) {
        // 先将新元素放入临时队列
        self.tmp.push_back(x);
        // 将主队列中所有元素搬到临时队列（此时临时队列顺序为 [x, old_top, ..., old_bottom]）
        while let Some(v) = self.main.pop_front() {
            self.tmp.push_back(v);
        }
        // 交换主队列和临时队列，此时主队列即为栈的正确顺序
        std::mem::swap(&mut self.main, &mut self.tmp);
    }

    /// 弹出栈顶元素。时间复杂度 O(1)
    pub fn pop(&mut self) -> i32 {
        self.main.pop_front().expect("stack is empty")
    }

    /// 获取栈顶元素（不弹出）。时间复杂度 O(1)
    pub fn top(&mut self) -> i32 {
        *self.main.front().expect("stack is empty")
    }

    /// 判断栈是否为空
    pub fn empty(&self) -> bool {
        self.main.is_empty()
    }
}
```
