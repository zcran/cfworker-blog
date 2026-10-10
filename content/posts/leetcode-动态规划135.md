---
title: "leetcode-动态规划135"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 找出有效子序列的最大长度 II

给你一个整数数组 nums 和一个 正 整数 k 。

nums 的一个 子序列 sub 的长度为 x ，如果其满足以下条件，则称其为 有效子序列 ：

(sub[0] + sub[1]) % k == (sub[1] + sub[2]) % k == ... == (sub[x - 2] + sub[x - 1]) % k

返回 nums 的 最长有效子序列 的长度。


```
impl Solution {
    pub fn maximum_length(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;

        // dp[a][b] 表示以模 k 余数对 (a, b) 交替出现的最长有效子序列长度。
        // 由于 (sub[i] + sub[i+1]) % k 恒定，相邻两项的余数对会交替反转，
        // 因此只需在遇到余数 r 时，更新所有以 (prev, r) 结尾的状态。
        let mut dp = vec![vec![0i32; k]; k];
        let mut ans = 0i32;

        for num in nums {
            let r = (num % k as i32) as usize;

            for prev in 0..k {
                // 将当前 num 接到以 (r, prev) 结尾的子序列后面
                let len = dp[r][prev] + 1;
                dp[prev][r] = len;
                ans = ans.max(len);
            }
        }

        ans
    }
}
```
