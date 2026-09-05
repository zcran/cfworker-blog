---
title: "leetcode-前缀和9"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最大平均值和的分组

给定数组 nums 和一个整数 k 。我们将给定的数组 nums 分成 最多 k 个非空子数组，且数组内部是连续的 。 分数 由每个子数组内的平均值的总和构成。

注意我们必须使用 nums 数组中的每一个数进行分组，并且分数不一定需要是整数。

返回我们所能得到的最大 分数 是多少。答案误差在 10-6 内被视为是正确的。


```
impl Solution {
    pub fn largest_sum_of_averages(nums: Vec<i32>, k: i32) -> f64 {
        let n = nums.len();
        let k = k as usize;

        // 前缀和，便于快速计算子数组和
        let mut prefix = vec![0i32; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i];
        }

        // dp[i][j]：前 i 个元素分成 j 段的最大平均和
        // 使用滚动数组优化空间，只保留当前段数 j 和上一段数 j-1
        let mut dp = vec![0.0; n + 1];
        let mut prev = vec![0.0; n + 1];

        // 初始化：分成 1 段的情况
        for i in 1..=n {
            dp[i] = prefix[i] as f64 / i as f64;
        }

        // 枚举段数
        for j in 2..=k {
            // 交换，使得 prev 指向 j-1 段的结果，dp 用于计算 j 段
            std::mem::swap(&mut dp, &mut prev);

            // 至少需要 j 个元素
            for i in j..=n {
                let mut max_val = 0.0;
                // 最后一段从 l 到 i-1
                for l in j - 1..i {
                    let avg = (prefix[i] - prefix[l]) as f64 / (i - l) as f64;
                    let val = prev[l] + avg;
                    if val > max_val {
                        max_val = val;
                    }
                }
                dp[i] = max_val;
            }
        }

        dp[n]
    }
}
```
