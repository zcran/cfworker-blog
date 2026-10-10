---
title: "leetcode-动态规划17"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 戳气球

有 n 个气球，编号为0 到 n - 1，每个气球上都标有一个数字，这些数字存在数组 nums 中。

现在要求你戳破所有的气球。戳破第 i 个气球，你可以获得 nums[i - 1] * nums[i] * nums[i + 1] 枚硬币。 这里的 i - 1 和 i + 1 代表和 i 相邻的两个气球的序号。如果 i - 1或 i + 1 超出了数组的边界，那么就当它是一个数字为 1 的气球。

求所能获得硬币的最大数量。




```
impl Solution {
    pub fn max_coins(mut nums: Vec<i32>) -> i32 {
        // 在数组首尾各插入一个 1，作为虚拟边界气球
        nums.insert(0, 1);
        nums.push(1);
        let n = nums.len();

        // dp[i][j]：戳破开区间 (i, j) 内所有气球能获得的最大硬币数
        // 按区间长度从小到大递推，保证子问题先于大问题求解
        let mut dp = vec![vec![0; n]; n];

        for len in 2..n {
            for i in 0..n - len {
                let j = i + len;
                // k 为区间 (i, j) 中最后一个被戳破的气球
                for k in i + 1..j {
                    let coins = nums[i] * nums[k] * nums[j] + dp[i][k] + dp[k][j];
                    dp[i][j] = dp[i][j].max(coins);
                }
            }
        }

        // 整个区间为 (0, n-1)，两端是虚拟边界气球
        dp[0][n - 1]
    }
}
```
