---
title: "leetcode-滑动窗口12"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最小区间

你有 k 个 非递减排列 的整数列表。找到一个 最小 区间，使得 k 个列表中的每个列表至少有一个数包含在其中。

我们定义如果 b-a < d-c 或者在 b-a == d-c 时 a < c，则区间 [a,b] 比 [c,d] 小。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn smallest_range(nums: Vec<Vec<i32>>) -> Vec<i32> {
        let k = nums.len();                         // 列表个数
        let mut pointers = vec![0; k];              // 每个列表当前指向的索引
        let mut max_val = i32::MIN;                 // 当前窗口中的最大值
        let mut min_heap = BinaryHeap::new();       // 最小堆，存储 (元素值, 列表索引)

        // 初始化：每个列表的第一个元素入堆，并记录最大值
        for (i, list) in nums.iter().enumerate() {
            let val = list[0];
            max_val = max_val.max(val);
            min_heap.push(Reverse((val, i)));
        }

        let mut best_left = 0;
        let mut best_right = i32::MAX;

        // 每次弹出当前最小值，尝试缩小窗口
        while let Some(Reverse((min_val, row))) = min_heap.pop() {
            // 当前窗口 [min_val, max_val] 覆盖所有列表，更新最优区间
            if max_val - min_val < best_right - best_left {
                best_left = min_val;
                best_right = max_val;
            }

            // 如果当前列表已遍历完，无法再继续，终止
            if pointers[row] + 1 == nums[row].len() {
                break;
            }

            // 当前列表指针后移，取下一个元素
            pointers[row] += 1;
            let next_val = nums[row][pointers[row]];
            max_val = max_val.max(next_val);        // 更新窗口最大值
            min_heap.push(Reverse((next_val, row))); // 新元素入堆
        }

        vec![best_left, best_right]
    }
}
```
