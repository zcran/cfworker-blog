---
title: "leetcode-哈希表94"
date: 2026-10-09T20:08:02+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 至多一次替换后的最大相邻相等元素对数

给你一个 下标从 1 开始 的整数数组 nums。

你可以选择两个 不同 的值 x 和 y，并 最多 执行一次以下操作：

将 nums 中所有值为 x 的元素替换为 y。

返回执行操作后，相邻且相等的元素对数量的 最大值 。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_equal_adjacent_pairs(nums: Vec<i32>) -> i32 {
        // base：原本就相邻且相等的对数
        // max_cnt：把某个值统一替换后，能额外凑成的"相邻相等对"数量的最大值
        let mut base = 0;
        let mut max_cnt = 0;
        let mut cnt: HashMap<(i32, i32), i32> = HashMap::new();

        for w in nums.windows(2) {
            let (a, b) = (w[0], w[1]);
            if a == b {
                base += 1;
            } else {
                // 把 (a, b) 与 (b, a) 统一为同一键（小值在前）
                let key = if a < b { (a, b) } else { (b, a) };
                let e = cnt.entry(key).or_insert(0);
                *e += 1;
                max_cnt = max_cnt.max(*e);
            }
        }

        // 一次替换最多影响一对相邻位置；
        // 原本相等的对不会被破坏，再加上额外能凑出的最大值
        base + max_cnt
    }
}
```
