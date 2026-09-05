---
title: "leetcode-前缀和57"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计隐藏数组数目

给你一个下标从 0 开始且长度为 n 的整数数组 differences ，它表示一个长度为 n + 1 的 隐藏 数组 相邻 元素之间的 差值 。更正式的表述为：我们将隐藏数组记作 hidden ，那么 differences[i] = hidden[i + 1] - hidden[i] 。

同时给你两个整数 lower 和 upper ，它们表示隐藏数组中所有数字的值都在 闭 区间 [lower, upper] 之间。

比方说，differences = [1, -3, 4] ，lower = 1 ，upper = 6 ，那么隐藏数组是一个长度为 4 且所有值都在 1 和 6 （包含两者）之间的数组。
    [3, 4, 1, 5] 和 [4, 5, 2, 6] 都是符合要求的隐藏数组。
    [5, 6, 3, 7] 不符合要求，因为它包含大于 6 的元素。
    [1, 2, 3, 4] 不符合要求，因为相邻元素的差值不符合给定数据。

请你返回 符合 要求的隐藏数组的数目。如果没有符合要求的隐藏数组，请返回 0 。


```
impl Solution {
    pub fn number_of_arrays(differences: Vec<i32>, lower: i32, upper: i32) -> i32 {
        // 设隐藏数组第一个元素为 h0，则 hidden[i] = h0 + prefix[i]
        // 其中 prefix[i] 是 differences[0..i] 的前缀和
        // 所有 hidden[i] 必须在 [lower, upper] 范围内
        // 即 lower <= h0 + prefix[i] <= upper 对所有 i 成立
        // 等价于: lower - min(prefix) <= h0 <= upper - max(prefix)
        // 因此 h0 的可选数量为 (upper - lower) - (max(prefix) - min(prefix)) + 1

        let mut min_prefix = 0;  // 前缀和的最小值
        let mut max_prefix = 0;  // 前缀和的最大值
        let mut curr_prefix = 0;

        for &d in &differences {
            curr_prefix += d;
            min_prefix = min_prefix.min(curr_prefix);
            max_prefix = max_prefix.max(curr_prefix);

            // 如果前缀范围已经超过允许范围，则无解
            if max_prefix - min_prefix > upper - lower {
                return 0;
            }
        }

        // 返回 h0 的可选数量
        (upper - lower) - (max_prefix - min_prefix) + 1
    }
}
```
