---
title: "leetcode-设计37"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 连续中值

随机产生数字并传递给一个方法。你能否完成这个方法，在每次产生新值时，寻找当前所有值的中间值（中位数）并保存。

中位数是有序列表中间的数。如果列表长度是偶数，中位数则是中间两个数的平均值。

例如，

[2,3,4] 的中位数是 3

[2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

void addNum(int num) - 从数据流中添加一个整数到数据结构中。

double findMedian() - 返回目前所有元素的中位数。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

/// 中位数查找器
///
/// 维护两个堆：大顶堆（存储较小的一半）和小顶堆（存储较大的一半）
/// 保持大顶堆大小 >= 小顶堆大小，且差值不超过1
/// 时间复杂度: add O(log n), find O(1)
/// 空间复杂度: O(n)
struct MedianFinder {
    max_heap: BinaryHeap<i32>,          // 存储较小的一半（大顶堆）
    min_heap: BinaryHeap<Reverse<i32>>, // 存储较大的一半（小顶堆）
}

impl MedianFinder {
    /// 初始化中位数查找器
    fn new() -> Self {
        MedianFinder {
            max_heap: BinaryHeap::new(),
            min_heap: BinaryHeap::new(),
        }
    }

    /// 添加一个数字到数据流
    fn add_num(&mut self, num: i32) {
        // 1. 选择插入哪个堆：默认插入大顶堆（较小的一半）
        if self.max_heap.is_empty() || num <= *self.max_heap.peek().unwrap() {
            self.max_heap.push(num);
        } else {
            self.min_heap.push(Reverse(num));
        }

        // 2. 平衡两个堆的大小（保持大顶堆 >= 小顶堆，且差值 <= 1）
        if self.max_heap.len() > self.min_heap.len() + 1 {
            // 大顶堆太大：移一个到小顶堆
            let top = self.max_heap.pop().unwrap();
            self.min_heap.push(Reverse(top));
        } else if self.min_heap.len() > self.max_heap.len() {
            // 小顶堆太大：移一个到大顶堆
            let Reverse(top) = self.min_heap.pop().unwrap();
            self.max_heap.push(top);
        }
    }

    /// 返回当前所有元素的中位数
    fn find_median(&self) -> f64 {
        if self.max_heap.len() > self.min_heap.len() {
            // 奇数个元素：中位数在大顶堆顶部
            *self.max_heap.peek().unwrap() as f64
        } else {
            // 偶数个元素：中位数为两个堆顶的平均值
            let left = *self.max_heap.peek().unwrap() as f64;
            let right = self.min_heap.peek().unwrap().0 as f64;
            (left + right) / 2.0
        }
    }
}
```
