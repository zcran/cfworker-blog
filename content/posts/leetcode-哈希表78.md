---
title: "leetcode-哈希表78"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 重排完成顺序

给你一个长度为 n 的整数数组 order 和一个整数数组 friends。

order 包含从 1 到 n 的每个整数，且 恰好出现一次 ，表示比赛中参赛者按照 完成顺序 的 ID。

friends 包含你朋友们的 ID，按照 严格递增 的顺序排列。friends 中的每个 ID 都保证出现在 order 数组中。

请返回一个数组，包含你朋友们的 ID，按照他们的 完成顺序 排列。


```
impl Solution {
    pub fn recover_order(order: Vec<i32>, friends: Vec<i32>) -> Vec<i32> {
        // 标记哪些 ID 是朋友（ID 范围 1..=n）
        let n = order.len();
        let mut is_friend = vec![false; n + 1];
        for &x in &friends {
            is_friend[x as usize] = true;
        }

        // 按 order 的顺序筛选出朋友 ID，天然即为完成顺序
        order
            .into_iter()
            .filter(|&x| is_friend[x as usize])
            .collect()
    }
}
```
