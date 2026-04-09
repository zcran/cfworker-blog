---
title: "leetcode-数据流3"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
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

struct KthLargest {
    nums: BinaryHeap<Reverse<i32>>,
    k: usize,
}

impl KthLargest {
    fn new(k: i32, data: Vec<i32>) -> Self {
        let k = k as usize;
        let nums = data.into_iter()
            .fold(BinaryHeap::new(), |mut heap, num| {
                heap.push(Reverse(num));
                if heap.len() > k {
                    heap.pop();
                }
                heap
            });

        Self { nums, k }
    }


    fn add(&mut self, val: i32) -> i32 {
        self.nums.push(Reverse(val));
        if self.nums.len() > self.k {
            self.nums.pop();
        }
        self.nums.peek().unwrap().0
    }
}
```


为什么要用最小堆？ 要找第K大的元素，只需要维护前K大的元素，堆顶就是这K个元素中最小的（即第K大的元素）
Reverse<T>：Rust标准库提供的包装类型，实现反向排序，让最大堆变成最小堆
维护策略：始终保持堆的大小不超过K
