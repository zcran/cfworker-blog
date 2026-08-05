---
title: "leetcode-设计14"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 餐盘栈

我们把无限数量 ∞ 的栈排成一行，按从左到右的次序从 0 开始编号。每个栈的的最大容量 capacity 都相同。

实现一个叫「餐盘」的类 DinnerPlates：

DinnerPlates(int capacity) - 给出栈的最大容量 capacity。
void push(int val) - 将给出的正整数 val 推入 从左往右第一个 没有满的栈。
int pop() - 返回 从右往左第一个 非空栈顶部的值，并将其从栈中删除；如果所有的栈都是空的，请返回 -1。
int popAtStack(int index) - 返回编号 index 的栈顶部的值，并将其从栈中删除；如果编号 index 的栈是空的，请返回 -1。


```
use std::collections::{BinaryHeap, HashMap};
use std::cmp::max;

/// 餐盘栈：支持从左到右填充、从右到左弹出、按索引弹出
struct DinnerPlates {
    /// 栈索引 -> 栈内容（栈顶在末尾）
    stack_map: HashMap<i32, Vec<i32>>,
    /// 最小堆（通过存储负数实现），记录所有未满栈的索引
    ///
    /// 存储 -index，使得堆顶是绝对值最小的索引（即最靠左的未满栈）
    available: BinaryHeap<i32>,
    /// 当前最右侧的栈索引（可能对应空栈）
    rightmost: i32,
    /// 每个栈的最大容量
    capacity: usize,
}

impl DinnerPlates {
    /// 初始化，指定每个栈的最大容量
    pub fn new(capacity: i32) -> Self {
        let capacity = capacity as usize;
        Self {
            stack_map: HashMap::new(),
            available: BinaryHeap::new(),
            rightmost: 0,
            capacity,
        }
    }

    /// 将元素推入从左到右第一个未满的栈
    ///
    /// 时间复杂度: O(log n)，n 为未满栈的数量
    pub fn push(&mut self, val: i32) {
        // 尝试从堆中获取最靠左的未满栈索引
        if let Some(mut idx_neg) = self.available.pop() {
            // 还原为正索引
            let idx = -idx_neg;
            // 更新最右侧索引
            self.rightmost = max(self.rightmost, idx);

            // 获取目标栈，若不存在则创建
            let stack = self.stack_map.entry(idx).or_insert_with(Vec::new);
            stack.push(val);
        } else {
            // 没有可用栈，使用当前最右侧栈或创建新栈
            let stack = self.stack_map.entry(self.rightmost).or_insert_with(Vec::new);

            if stack.len() != self.capacity {
                // 当前栈未满，直接使用
                stack.push(val);
            } else {
                // 当前栈已满，创建新栈
                self.rightmost += 1;
                self.stack_map.insert(self.rightmost, vec![val]);
            }
        }
    }

    /// 从右往左弹出第一个非空栈的顶部元素
    ///
    /// 时间复杂度: O(1) 均摊
    pub fn pop(&mut self) -> i32 {
        // 从右向左查找第一个非空栈
        while let Some(stack) = self.stack_map.get_mut(&self.rightmost) {
            if let Some(val) = stack.pop() {
                // 弹出成功，将当前栈索引加入可用队列（因为可能未满）
                self.available.push(-self.rightmost);
                return val;
            }
            // 当前栈为空，继续向左查找
            self.rightmost -= 1;
        }

        // 没有非空栈，重置索引并返回 -1
        self.rightmost = 0;
        -1
    }

    /// 弹出指定索引栈的顶部元素
    ///
    /// 时间复杂度: O(log n)
    pub fn pop_at_stack(&mut self, index: i32) -> i32 {
        if let Some(stack) = self.stack_map.get_mut(&index) {
            if let Some(val) = stack.pop() {
                // 弹出成功，将当前栈索引加入可用队列
                self.available.push(-index);
                return val;
            }
        }
        -1
    }
}
```
