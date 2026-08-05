---
title: "leetcode-枚举57"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 执行操作使数据元素之和大于等于 K

给你一个正整数 k 。最初，你有一个数组 nums = [1] 。

你可以对数组执行以下 任意 操作 任意 次数（可能为零）：

选择数组中的任何一个元素，然后将它的值 增加 1 。
复制数组中的任何一个元素，然后将它附加到数组的末尾。

返回使得最终数组元素之 和 大于或等于 k 所需的 最少 操作次数。


```
impl Solution {
    pub fn min_operations(k: i32) -> i32 {
        // 设最终数组长度为 m（通过复制操作得到 m-1 次）
        // 每个元素最大值为 base + 1（通过增加操作）
        // 需要满足：m * (base + 1) >= k
        // 总操作数 = (m - 1) + base
        // 对于每个 m，base = ceil(k/m) - 1
        // 总操作数 = (m - 1) + ceil(k/m) - 1 = m + ceil(k/m) - 2

        let mut ans = k - 1; // 只使用增加操作，不复制

        // 枚举可能的数组长度 m
        // m 的平方根是最优平衡点，因为 m + k/m 在 sqrt(k) 处取得最小值
        let limit = (k as f64).sqrt() as i32 + 1;

        for m in 1..=limit {
            // 需要每个元素的平均值至少为 ceil(k/m)
            let base = (k + m - 1) / m; // ceil(k/m)
            // 操作数 = (m - 1) + (base - 1)
            let ops = m + base - 2;
            ans = ans.min(ops);
        }

        ans
    }
}
```
