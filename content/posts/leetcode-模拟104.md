---
title: "leetcode-模拟104"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 超过阈值的最少操作数 II

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。

你可以对 nums 执行一些操作，在一次操作中，你可以：

· 选择 nums 中 最小 的两个整数 x 和 y 。
· 将 x 和 y 从 nums 中删除。
· 将 min(x, y) * 2 + max(x, y) 添加到数组中的任意位置。

注意，只有当 nums 至少 包含两个元素时，你才可以执行以上操作。

你需要使数组中的所有元素都 大于或等于 k ，请你返回需要的 最少 操作次数。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn min_operations(nums: Vec<i32>, k: i32) -> i32 {
        let mut pq: BinaryHeap<Reverse<i64>> = nums
            .into_iter()
            .map(|x| Reverse(x as i64))
            .collect();
        let mut ops = 0;
        let k = k as i64;

        // 每次取两个最小元素合并，直到堆顶 >= k
        while let (Some(Reverse(x)), Some(Reverse(y))) = (pq.pop(), pq.pop()) {
            if x >= k {
                break;  // 最小元素已满足条件，全部 >= k
            }
            pq.push(Reverse(x * 2 + y));
            ops += 1;
        }

        ops
    }
}
```
