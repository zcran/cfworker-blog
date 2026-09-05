---
title: "leetcode-前缀和80"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 重排数组以得到最大前缀分数

给你一个下标从 0 开始的整数数组 nums 。你可以将 nums 中的元素按 任意顺序 重排（包括给定顺序）。

令 prefix 为一个数组，它包含了 nums 重新排列后的前缀和。换句话说，prefix[i] 是 nums 重新排列后下标从 0 到 i 的元素之和。nums 的 分数 是 prefix 数组中正整数的个数。

返回可以得到的最大分数。


```
impl Solution {
    pub fn max_score(mut nums: Vec<i32>) -> i32 {
        // 降序排列，使前缀和尽可能大
        nums.sort_unstable_by(|a, b| b.cmp(a));

        let mut prefix_sum = 0i64;

        // 遍历排序后的数组，计算前缀和
        for (i, &num) in nums.iter().enumerate() {
            prefix_sum += num as i64;
            // 如果前缀和 <= 0，后续不可能再变成正数（因为后续元素更小）
            if prefix_sum <= 0 {
                return i as i32;
            }
        }

        // 所有前缀和都为正数
        nums.len() as i32
    }
}
```
