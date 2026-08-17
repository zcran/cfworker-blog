---
title: "leetcode-计数86"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 使数组为空的最少操作次数

给你一个下标从 0 开始的正整数数组 nums 。

你可以对数组执行以下两种操作 任意次 ：

从数组中选择 两个 值 相等 的元素，并将它们从数组中 删除 。

从数组中选择 三个 值 相等 的元素，并将它们从数组中 删除 。

请你返回使数组为空的 最少 操作次数，如果无法达成，请返回 -1 。




```
use std::collections::HashMap;

impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        // 统计每个数字的出现次数
        let mut freq = HashMap::with_capacity(nums.len());
        for &x in &nums {
            *freq.entry(x).or_insert(0) += 1;
        }

        let mut ops = 0;
        for &cnt in freq.values() {
            // 若某个数字只出现一次，无法通过删除2个或3个来清空
            if cnt == 1 {
                return -1;
            }

            // 贪心策略：优先用3个一组删除
            // 对于任意 cnt >= 2，最少操作数 = ceil(cnt / 3)
            // 但 cnt=2 时 ceil(2/3)=1，cnt=4 时 ceil(4/3)=2（拆成2+2）
            // 公式 (cnt + 2) / 3 对所有 cnt >= 2 都成立
            ops += (cnt + 2) / 3;
        }

        ops
    }
}
```
