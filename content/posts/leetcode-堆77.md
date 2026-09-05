---
title: "leetcode-堆75"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 数据流中的中位数

中位数 是有序整数列表中的中间值。如果列表的大小是偶数，则没有中间值，中位数是两个中间值的平均值。

例如，
[2,3,4] 的中位数是 3
[2,3] 的中位数是 (2 + 3) / 2 = 2.5
设计一个支持以下两种操作的数据结构：

void addNum(int num) - 从数据流中添加一个整数到数据结构中。

double findMedian() - 返回目前所有元素的中位数。

```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

/// 数据流中位数查找器
///
/// 核心思路：维护两个堆
/// - 最大堆（`left`）：存储较小的一半元素，堆顶是这部分的最大值
/// - 最小堆（`right`）：存储较大的一半元素，堆顶是这部分的最小值
/// - 保证 left 的大小等于 right 或比 right 多 1
/// - 中位数：如果 left 多 1，取 left 堆顶；否则取两个堆顶的平均值
pub struct MedianFinder {
    left: BinaryHeap<i32>,              // 最大堆，存较小的一半
    right: BinaryHeap<Reverse<i32>>,    // 最小堆，存较大的一半
}

impl MedianFinder {
    /// 初始化中位数查找器
    pub fn new() -> Self {
        Self {
            left: BinaryHeap::new(),
            right: BinaryHeap::new(),
        }
    }

    /// 添加一个整数到数据流
    ///
    /// 策略：
    /// 1. 新元素先放入左堆（较小的一半）
    /// 2. 如果左堆比右堆多 2 个，平衡一次
    /// 3. 如果左堆堆顶 > 右堆堆顶，交换堆顶元素确保左堆元素 ≤ 右堆元素
    pub fn add_num(&mut self, num: i32) {
        // 先放入左堆
        self.left.push(num);

        // 平衡大小：左堆最多比右堆多 1
        if self.left.len() > self.right.len() + 1 {
            let val = self.left.pop().unwrap();
            self.right.push(Reverse(val));
        }

        // 确保左堆所有元素 ≤ 右堆所有元素
        if let Some(&max_left) = self.left.peek() {
            if let Some(&Reverse(min_right)) = self.right.peek() {
                if max_left > min_right {
                    // 交换堆顶
                    let val_left = self.left.pop().unwrap();
                    let val_right = self.right.pop().unwrap().0;
                    self.left.push(val_right);
                    self.right.push(Reverse(val_left));
                }
            }
        }
    }

    /// 返回当前所有元素的中位数
    ///
    /// - 如果总数为奇数，返回左堆堆顶
    /// - 如果总数为偶数，返回两个堆顶的平均值
    pub fn find_median(&self) -> f64 {
        if self.left.len() > self.right.len() {
            // 奇数个元素，中位数在左堆堆顶
            *self.left.peek().unwrap() as f64
        } else {
            // 偶数个元素，取两个堆顶的平均值
            let left_max = *self.left.peek().unwrap() as f64;
            let right_min = self.right.peek().unwrap().0 as f64;
            (left_max + right_min) / 2.0
        }
    }
}
```
