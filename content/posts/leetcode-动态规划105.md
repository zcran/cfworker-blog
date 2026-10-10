---
title: "leetcode-动态规划105"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 检查数组是否存在有效划分

给你一个下标从 0 开始的整数数组 nums ，你必须将数组划分为一个或多个 连续 子数组。

如果获得的这些子数组中每个都能满足下述条件 之一 ，则可以称其为数组的一种 有效 划分：

1. 子数组 恰 由 2 个相等元素组成，例如，子数组 [2,2] 。
2. 子数组 恰 由 3 个相等元素组成，例如，子数组 [4,4,4] 。
3. 子数组 恰 由 3 个连续递增元素组成，并且相邻元素之间的差值为 1 。例如，子数组 [3,4,5] ，但是子数组 [1,3,5] 不符合要求。

如果数组 至少 存在一种有效划分，返回 true ，否则，返回 false 。


```
impl Solution {
    pub fn valid_partition(nums: Vec<i32>) -> bool {
        let n = nums.len();
        // dp[i]: nums[..i] 是否能有效划分
        let mut dp = vec![false; n + 1];
        dp[0] = true;

        for i in 2..=n {
            // 情形一：末尾两个相等元素
            if nums[i - 2] == nums[i - 1] && dp[i - 2] {
                dp[i] = true;
            }
            // 情形二/三：末尾三个元素，需 i >= 3
            if !dp[i] && i >= 3 {
                let (a, b, c) = (nums[i - 3], nums[i - 2], nums[i - 1]);
                if (a == b && b == c) || (a + 1 == b && b + 1 == c) {
                    dp[i] = dp[i - 3];
                }
            }
        }

        dp[n]
    }
}
```
