---
title: "leetcode-数据流7"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 最后 K 个数的乘积

设计一个算法，该算法接受一个整数流并检索该流中最后 k 个整数的乘积。

实现 ProductOfNumbers 类：

ProductOfNumbers() 用一个空的流初始化对象。
void add(int num) 将数字 num 添加到当前数字列表的最后面。
int getProduct(int k) 返回当前数字列表中，最后 k 个数字的乘积。你可以假设当前列表中始终 至少 包含 k 个数字。
题目数据保证：任何时候，任一连续数字序列的乘积都在 32 位整数范围内，不会溢出。


```
pub struct ProductOfNumbers {
    prefixes: Vec<i32>,
}

impl ProductOfNumbers {
    pub fn new() -> Self {
        Self { prefixes: vec![1] }
    }

    pub fn add(&mut self, num: i32) {
        // 使用 match 表达式替代 if
        match num {
            0 => self.prefixes.truncate(1),
            _ => self.prefixes.push(*self.prefixes.last().unwrap() * num),
        }
    }

    pub fn get_product(&self, k: i32) -> i32 {
        let k = k as usize;
        let n = self.prefixes.len();

        // 使用 match 和守卫模式
        match (k, n) {
            (k, n) if k >= n => 0,
            _ => self.prefixes[n - 1] / self.prefixes[n - 1 - k]
        }
    }
}
```
