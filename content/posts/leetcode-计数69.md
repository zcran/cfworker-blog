---
title: "leetcode-计数69"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 数组能形成多少数对

给你一个下标从 0 开始的整数数组 nums 。在一步操作中，你可以执行以下步骤：

从 nums 选出 两个 相等的 整数
从 nums 中移除这两个整数，形成一个 数对
请你在 nums 上多次执行此操作直到无法继续执行。

返回一个下标从 0 开始、长度为 2 的整数数组 answer 作为答案，其中 answer[0] 是形成的数对数目，answer[1] 是对 nums 尽可能执行上述操作后剩下的整数数目。




```
impl Solution {
    pub fn number_of_pairs(mut nums: Vec<i32>) -> Vec<i32> {
        // 排序后，相等的元素会相邻
        nums.sort_unstable();

        let mut pairs = 0;
        let mut i = 0;
        let n = nums.len();

        // 一次遍历，每次跳过已配对的元素
        while i + 1 < n {
            if nums[i] == nums[i + 1] {
                pairs += 1;
                i += 2; // 配对后跳过这两个元素
            } else {
                i += 1; // 不相等，当前元素无法配对
            }
        }

        // 剩余元素 = 总数 - 已配对的元素个数 (2 * pairs)
        vec![pairs, n as i32 - pairs * 2]
    }
}
```
