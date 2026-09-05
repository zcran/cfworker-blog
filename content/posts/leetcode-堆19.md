---
title: "leetcode-堆19"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 多次求和构造目标数组

给你一个整数数组 target 。一开始，你有一个数组 A ，它的所有元素均为 1 ，你可以执行以下操作：

令 x 为你数组里所有元素的和
选择满足 0 <= i < target.size 的任意下标 i ，并让 A 数组里下标为 i 处的值为 x 。
你可以重复该过程任意次

如果能从 A 开始构造出目标数组 target ，请你返回 True ，否则返回 False 。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 逆向贪心：从 target 倒推回全 1 数组
    pub fn is_possible(target: Vec<i32>) -> bool {
        if target.len() == 1 {
            return target[0] == 1;
        }

        let mut sum: i64 = target.iter().map(|&x| x as i64).sum();
        let mut heap = BinaryHeap::from(target.into_iter().map(|x| x as i64).collect::<Vec<_>>());

        while let Some(&max) = heap.peek() {
            if max == 1 {
                return true; // 所有元素都是 1
            }

            heap.pop();
            sum -= max; // sum 为其余元素之和

            // 无法继续：只有一个元素且大于 1，或 max 不大于其余元素之和
            if sum == 0 || max <= sum {
                return false;
            }

            // 快速回退多步：max 多次减去 sum，等价于取模
            let prev = (max - 1) % sum + 1;
            sum += prev;
            heap.push(prev);
        }

        true
    }
}
```
