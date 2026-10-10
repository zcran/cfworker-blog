---
title: "leetcode-哈希表63"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 使循环数组所有元素相等的最少秒数

给你一个下标从 0 开始长度为 n 的数组 nums 。

每一秒，你可以对数组执行以下操作：

对于范围在 [0, n - 1] 内的每一个下标 i ，将 nums[i] 替换成 nums[i] ，nums[(i - 1 + n) % n] 或者 nums[(i + 1) % n] 三者之一。

注意，所有元素会被同时替换。

请你返回将数组 nums 中所有元素变成相等元素所需要的 最少 秒数。


```
use std::collections::HashMap;

impl Solution {
    pub fn minimum_seconds(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 记录每个值出现的所有下标
        let mut positions: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, &x) in nums.iter().enumerate() {
            positions.entry(x).or_default().push(i);
        }

        // 对每个值，考虑其在环上的位置分布：
        // 若相邻两次出现之间的最大间隔为 d（按环长补齐后），
        // 则需要 d / 2 秒即可让所有元素变为该值。
        let mut best = usize::MAX;
        for pos in positions.values() {
            let m = pos.len();
            let mut max_gap = 0usize;

            for k in 0..m {
                let cur = pos[k];
                let nxt = if k + 1 < m { pos[k + 1] } else { pos[0] + n };
                max_gap = max_gap.max(nxt - cur);
            }

            let secs = max_gap / 2;
            if secs < best {
                best = secs;
            }
        }

        best as i32
    }
}
```
