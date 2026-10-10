---
title: "leetcode-动态规划63"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 分隔数组以得到最大和

给你一个整数数组 arr，请你将该数组分隔为长度 最多 为 k 的一些（连续）子数组。分隔完成后，每个子数组的中的所有值都会变为该子数组中的最大值。

返回将数组分隔变换后能够得到的元素最大和。本题所用到的测试用例会确保答案是一个 32 位整数。




```
impl Solution {
    /// 1043. 分隔数组以得到最大和
    ///
    /// dp[i]：将 nums[0..=i] 分隔后能得到的最大和。
    /// 对每个 i，枚举最后一段的长度 cnt（1..=min(k, i+1)），
    /// 该段覆盖下标 [i-cnt+1, i]，段内全变为其最大值 max_val，
    /// 贡献为 max_val * cnt，加上前面部分的 dp[i-cnt]。
    ///
    /// 内层一边向左扩展一边维护 max_val，整体 O(n*k)。
    pub fn max_sum_after_partitioning(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;
        // dp[i] 表示前 i 个元素（下标 0..i-1）分隔后的最大和，dp[0] = 0
        let mut dp = vec![0i32; n + 1];

        for i in 1..=n {
            let mut max_val = 0;
            // 最后一段长度为 len，起点为 i - len
            for len in 1..=k.min(i) {
                max_val = max_val.max(nums[i - len]);
                dp[i] = dp[i].max(dp[i - len] + max_val * len as i32);
            }
        }

        dp[n]
    }
}
```
