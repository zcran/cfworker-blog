---
title: "leetcode-二叉搜索树34"
date: 2026-05-14T20:16:16+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 数据流中的第 K 大元素

设计一个找到数据流中第 k 大元素的类（class）。注意是排序后的第 k 大元素，不是第 k 个不同的元素。

请实现 KthLargest 类：

KthLargest(int k, int[] nums) 使用整数 k 和整数流 nums 初始化对象。
int add(int val) 将 val 插入数据流 nums 后，返回当前数据流中第 k 大的元素。

```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

/// 维护一个大小为 k 的最小堆，用于在数据流中随时获取第 k 大的元素
pub struct KthLargest {
    k: usize,
    heap: BinaryHeap<Reverse<i32>>,
}

impl KthLargest {
    /// 创建新的 KthLargest 实例
    /// - `k`: 需要第 k 大（1-indexed）
    /// - `nums`: 初始数据流
    pub fn new(k: i32, nums: Vec<i32>) -> Self {
        let k = k as usize;
        // 将所有元素放入堆中，并仅保留最大的 k 个（即最小堆中的前 k 个）
        let mut heap = BinaryHeap::from_iter(nums.into_iter().map(Reverse));
        while heap.len() > k {
            heap.pop();
        }
        Self { k, heap }
    }

    /// 添加一个新元素到数据流，并返回当前第 k 大的元素
    /// 时间复杂度 O(log k)，空间复杂度 O(1) 额外
    pub fn add(&mut self, val: i32) -> i32 {
        self.heap.push(Reverse(val));
        if self.heap.len() > self.k {
            self.heap.pop();
        }
        // 堆顶即为第 k 大元素（题目保证 k >= 1 且调用时堆非空）
        self.heap.peek().unwrap().0
    }
}
```
