---
title: "leetcode-有序集合11"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 最大频率栈

设计一个类似堆栈的数据结构，将元素推入堆栈，并从堆栈中弹出出现频率最高的元素。

实现 FreqStack 类:

· FreqStack() 构造一个空的堆栈。
· void push(int val) 将一个整数 val 压入栈顶。
· int pop() 删除并返回堆栈中出现频率最高的元素。
    · 如果出现频率最高的元素不只一个，则移除并返回最接近栈顶的元素。



```
use std::collections::HashMap;

/// 频率栈数据结构
///
/// 每次pop返回出现频率最高的元素，若频率相同则返回最接近栈顶的
#[derive(Default)]
pub struct FreqStack {
    /// 当前最大频率
    max_freq: usize,
    /// 频率 -> 该频率下的元素栈（从栈顶到栈底）
    freq_stack: HashMap<usize, Vec<i32>>,
    /// 元素 -> 当前频率
    freq_map: HashMap<i32, usize>,
}

impl FreqStack {
    /// 创建一个空的频率栈
    pub fn new() -> Self {
        Self::default()
    }

    /// 将元素压入栈中
    ///
    /// # 时间复杂度
    /// O(1)
    pub fn push(&mut self, val: i32) {
        // 更新元素频率
        let freq = self.freq_map.entry(val).or_insert(0);
        *freq += 1;

        // 更新最大频率
        if *freq > self.max_freq {
            self.max_freq = *freq;
        }

        // 将元素添加到对应频率的栈中
        self.freq_stack.entry(*freq).or_default().push(val);
    }

    /// 弹出频率最高的元素
    ///
    /// # 时间复杂度
    /// O(1)
    ///
    /// # Panics
    /// 当栈为空时调用会panic
    pub fn pop(&mut self) -> i32 {
        // 从最高频率的栈中弹出栈顶元素
        let stack = self.freq_stack.get_mut(&self.max_freq)
            .expect("FreqStack is empty");
        let val = stack.pop().unwrap();

        // 更新元素频率
        let new_freq = self.max_freq - 1;
        *self.freq_map.get_mut(&val).unwrap() = new_freq;

        // 如果当前频率的栈为空，降低最大频率
        if stack.is_empty() {
            self.max_freq = new_freq;
        }

        val
    }

    /// 检查栈是否为空
    pub fn is_empty(&self) -> bool {
        self.max_freq == 0
    }

    /// 获取栈中元素数量
    pub fn len(&self) -> usize {
        self.freq_map.len()
    }
}
```
