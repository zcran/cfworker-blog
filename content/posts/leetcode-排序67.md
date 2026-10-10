---
title: "leetcode-排序67"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 修改数组后最大化数组中的连续元素数目

给你一个下标从 0 开始只包含 正 整数的数组 nums 。

一开始，你可以将数组中 任意数量 元素增加 至多 1 。

修改后，你可以从最终数组中选择 一个或者更多 元素，并确保这些元素升序排序后是 连续 的。比方说，[3, 4, 5] 是连续的，但是 [3, 4, 6] 和 [1, 1, 2, 3] 不是连续的。

请你返回 最多 可以选出的元素数目。




```
impl Solution {
    pub fn max_selected_elements(mut nums: Vec<i32>) -> i32 {
        if nums.is_empty() {
            return 0;
        }
        nums.sort_unstable();

        // prev0: 前一个元素不加 1 时，能形成的最长连续序列长度
        // prev1: 前一个元素加 1 时，能形成的最长连续序列长度
        let mut prev0 = 1;
        let mut prev1 = 1;
        let mut ans = 1;

        for i in 1..nums.len() {
            let d = nums[i] - nums[i - 1];

            // 当前元素不加 1
            let cur0 = match d {
                0 => prev0,        // 与前一个元素相同，不能同时选，继承 prev0
                1 => prev0 + 1,    // 前一个不加 1，当前不加 1，恰好连续
                2 => prev1 + 1,    // 前一个加 1，当前不加 1，恰好连续
                _ => 1,            // 其他情况无法接上，只能自己开始
            };

            // 当前元素加 1
            let cur1 = match d {
                0 => prev0 + 1,    // 前一个不加 1，当前加 1，恰好连续
                1 => prev1 + 1,    // 前一个加 1，当前加 1，恰好连续
                _ => 1,            // 其他情况无法接上
            };

            prev0 = cur0;
            prev1 = cur1;
            ans = ans.max(prev0).max(prev1);
        }

        ans
    }
}
```
