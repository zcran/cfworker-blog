---
title: "leetcode-二分查找31"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最长等差数列

给定一个整数数组 nums，返回 nums 中最长等差子序列的长度。

注意：

子序列 是由另一个数组删除一些（也可以不删除）元素后得到的数组，并且不改变剩余元素之间的相对顺序。

如果对于所有 0 <= i < seq.length - 1，seq[i + 1] - seq[i] 的值都相同，则称序列 seq 为等差序列。


```
impl Solution {
    /// 返回 nums 中最长等差子序列的长度。
    ///
    /// 思路：动态规划。
    ///   设 dp[j][d] 表示以 nums[j] 结尾、公差为 d 的等差子序列的最大长度。
    ///   对每个 j，枚举它之前的 i，计算公差 d = nums[j] - nums[i]，
    ///   则 dp[j][d] = dp[i][d] + 1。
    ///
    ///   由于公差范围是 [-500, 500]，统一加上偏移量 500 映射到下标。
    ///   初始长度为 1（单个元素），答案即为所有 dp 值的最大值。
    pub fn longest_arith_seq_length(nums: Vec<i32>) -> i32 {
        const OFFSET: usize = 500;
        const RANGE: usize = 1001;
        let n = nums.len();
        let mut dp = vec![vec![0i32; RANGE]; n];
        let mut max_len = 0;

        for j in 1..n {
            for i in 0..j {
                let d = (nums[j] - nums[i] + OFFSET as i32) as usize;
                // dp[i][d] 为 0 表示以 nums[i] 结尾、公差 d 的序列尚未出现，
                // 此时以 nums[j] 结尾的长度为 2（nums[i], nums[j]）
                dp[j][d] = dp[i][d] + 1;
                max_len = max_len.max(dp[j][d]);
            }
        }

        max_len + 1
    }
}
```
