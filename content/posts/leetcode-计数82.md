---
title: "leetcode-计数82"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 拆分数组的最小代价

给你一个整数数组 nums 和一个整数 k 。

将数组拆分成一些非空子数组。拆分的 代价 是每个子数组中的 重要性 之和。

令 trimmed(subarray) 作为子数组的一个特征，其中所有仅出现一次的数字将会被移除。

· 例如，trimmed([3,1,2,4,3,4]) = [3,4,3,4] 。

子数组的 重要性 定义为 k + trimmed(subarray).length 。

· 例如，如果一个子数组是 [1,2,3,3,3,4,4] ，trimmed([1,2,3,3,3,4,4]) = [3,3,3,4,4] 。这个子数组的重要性就是 k + 5 。

找出并返回拆分 nums 的所有可行方案中的最小代价。

子数组 是数组的一个连续 非空 元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn min_cost(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        // dp[i] 表示从位置 i 到末尾的最小代价
        let mut dp = vec![-1; n + 1];
        dp[n] = 0;

        // 从后往前动态规划，避免递归
        for start in (0..n).rev() {
            let mut freq = HashMap::with_capacity(n - start);
            let mut single_count = 0; // 当前子数组中只出现一次的数字个数
            let mut min_cost = i32::MAX;

            for end in start..n {
                let x = nums[end];
                let count = freq.get(&x).copied().unwrap_or(0);

                if count == 0 {
                    // 第一次出现，只出现一次的数字 +1
                    single_count += 1;
                } else if count == 1 {
                    // 第二次出现，这个数字不再只出现一次，single_count -1
                    single_count -= 1;
                }
                // count >= 2 时不影响 single_count
                freq.insert(x, count + 1);

                // 子数组长度 = end - start + 1
                // 重要性 = 子数组长度 - 只出现一次的数字个数 + k
                let importance = (end - start + 1) as i32 - single_count + k;
                let total = importance + dp[end + 1];
                if total < min_cost {
                    min_cost = total;
                }
            }
            dp[start] = min_cost;
        }

        dp[0]
    }
}
```
