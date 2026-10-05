---
title: "leetcode-二分查找42"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 得到子序列的最少操作次数

给你一个数组 target ，包含若干 互不相同 的整数，以及另一个整数数组 arr ，arr 可能 包含重复元素。

每一次操作中，你可以在 arr 的任意位置插入任一整数。比方说，如果 arr = [1,4,1,2] ，那么你可以在中间添加 3 得到 [1,4,3,1,2] 。你可以在数组最开始或最后面添加整数。

请你返回 最少 操作次数，使得 target 成为 arr 的一个子序列。

一个数组的 子序列 指的是删除原数组的某些元素（可能一个元素都不删除），同时不改变其余元素的相对顺序得到的数组。比方说，[2,7,4] 是 [4,2,3,7,2,1,4] 的子序列（加粗元素），但 [2,4,2] 不是子序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn min_operations(target: Vec<i32>, arr: Vec<i32>) -> i32 {
        // 记录 target 中每个值对应的下标
        let pos: HashMap<i32, usize> = target
            .iter()
            .enumerate()
            .map(|(i, &v)| (v, i))
            .collect();

        // tails 维护最长递增子序列的贪心最小末尾
        let mut tails: Vec<usize> = Vec::new();

        for &val in &arr {
            if let Some(&i) = pos.get(&val) {
                let j = tails.partition_point(|&v| v < i);
                if j == tails.len() {
                    tails.push(i);
                } else {
                    tails[j] = i;
                }
            }
        }

        // target 中无需插入的元素个数即为最长可匹配子序列长度
        (target.len() - tails.len()) as i32
    }
}
```
