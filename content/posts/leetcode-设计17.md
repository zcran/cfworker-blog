---
title: "leetcode-设计17"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计一个支持增量操作的栈

请你设计一个支持对其元素进行增量操作的栈。

实现自定义栈类 CustomStack ：

CustomStack(int maxSize)：用 maxSize 初始化对象，maxSize 是栈中最多能容纳的元素数量。
void push(int x)：如果栈还未增长到 maxSize ，就将 x 添加到栈顶。
int pop()：弹出栈顶元素，并返回栈顶的值，或栈为空时返回 -1 。
void inc(int k, int val)：栈底的 k 个元素的值都增加 val 。如果栈中元素总数小于 k ，则栈中的所有元素都增加 val 。


```
/// 支持增量操作的自定义栈
struct CustomStack {
    /// 栈内元素
    elements: Vec<i32>,
    /// 栈的最大容量
    max_size: usize,
}

impl CustomStack {
    /// 创建一个最大容量为 max_size 的空栈
    ///
    /// 时间复杂度: O(1)，空间复杂度: O(max_size)
    pub fn new(max_size: i32) -> Self {
        Self {
            elements: Vec::with_capacity(max_size as usize),
            max_size: max_size as usize,
        }
    }

    /// 将元素压入栈顶，如果栈未满
    ///
    /// 时间复杂度: O(1) 均摊
    pub fn push(&mut self, x: i32) {
        if self.elements.len() < self.max_size {
            self.elements.push(x);
        }
    }

    /// 弹出栈顶元素，如果栈为空则返回 -1
    ///
    /// 时间复杂度: O(1)
    pub fn pop(&mut self) -> i32 {
        self.elements.pop().unwrap_or(-1)
    }

    /// 将栈底 k 个元素增加 val，如果元素不足则全部增加
    ///
    /// 时间复杂度: O(min(k, n))，n 为栈的大小
    pub fn increment(&mut self, k: i32, val: i32) {
        // 取实际需要增加的元素个数
        let count = k as usize;
        let limit = count.min(self.elements.len());

        // 使用切片直接修改，更高效
        if let Some(range) = self.elements.get_mut(..limit) {
            for elem in range {
                *elem += val;
            }
        }
    }
}
```
