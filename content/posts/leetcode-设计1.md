---
title: "leetcode-设计1"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 最小栈

设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

实现 MinStack 类:

MinStack() 初始化堆栈对象。
void push(int value) 将元素 value 推入堆栈。
void pop() 删除堆栈顶部的元素。
int top() 获取堆栈顶部的元素。
int getMin() 获取堆栈中的最小元素。


```
struct MinStack {
    stack: Vec<(i32, i32)>,
}

impl MinStack {
    /// 创建一个新的 MinStack
    pub fn new() -> Self {
        MinStack {
            stack: Vec::with_capacity(16), // 预分配容量减少重分配
        }
    }

    /// 将元素压入栈顶，同时记录当前最小值
    pub fn push(&mut self, val: i32) {
        let min = if let Some(last) = self.stack.last() {
            last.1.min(val)
        } else {
            val // 第一个元素，最小值就是自身
        };
        self.stack.push((val, min));
    }

    /// 移除栈顶元素
    pub fn pop(&mut self) {
        self.stack.pop();
    }

    /// 返回栈顶元素的值
    pub fn top(&self) -> i32 {
        self.stack.last().unwrap().0
    }

    /// 返回栈中所有元素的最小值
    pub fn get_min(&self) -> i32 {
        self.stack.last().unwrap().1
    }
}
```
