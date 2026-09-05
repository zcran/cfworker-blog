---
title: "leetcode-前缀和101"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 特殊数组 II

如果数组的每一对相邻元素都是两个奇偶性不同的数字，则该数组被认为是一个 特殊数组 。

你有一个整数数组 nums 和一个二维整数矩阵 queries，对于 queries[i] = [fromi, toi]，请你帮助你检查 子数组 nums[fromi..toi] 是不是一个 特殊数组 。

返回布尔数组 answer，如果 nums[fromi..toi] 是特殊数组，则 answer[i] 为 true ，否则，answer[i] 为 false 。


```
impl Solution {
    pub fn is_array_special(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<bool> {
        let n = nums.len();
        // prefix[i] 表示从 0 到 i 的连续特殊前缀长度
        let mut prefix = vec![1; n];
        for i in 1..n {
            prefix[i] = if (nums[i] ^ nums[i - 1]) & 1 == 1 {
                prefix[i - 1] + 1
            } else {
                1
            };
        }

        queries
            .iter()
            .map(|q| {
                let (l, r) = (q[0] as usize, q[1] as usize);
                // 子数组长度 <= 从 r 开始向左连续特殊的最长长度
                prefix[r] >= (r - l + 1) as i32
            })
            .collect()
    }
}
```
