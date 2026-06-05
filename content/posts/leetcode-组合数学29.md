---
title: "leetcode-组合数学29"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 单调数组对的数目 II


给你一个长度为 n 的 正 整数数组 nums 。

如果两个 非负 整数数组 (arr1, arr2) 满足以下条件，我们称它们是 单调 数组对：

两个数组的长度都是 n 。
arr1 是单调 非递减 的，换句话说 arr1[0] <= arr1[1] <= ... <= arr1[n - 1] 。
arr2 是单调 非递增 的，换句话说 arr2[0] >= arr2[1] >= ... >= arr2[n - 1] 。
对于所有的 0 <= i <= n - 1 都有 arr1[i] + arr2[i] == nums[i] 。

请你返回所有 单调 数组对的数目。

由于答案可能很大，请你将它对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn count_of_pairs(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();
        // 数组元素的最大值，用于确定 DP 数组的大小
        let max_val = *nums.iter().max().unwrap() as usize;

        // prev[x] 表示处理到前一个位置时，arr2 取值为 x 的方案数
        let mut prev = vec![0i64; max_val + 1];
        // 初始化第一个位置：arr2[0] 可以取 0..=nums[0] 任意值
        for v in 0..=nums[0] as usize {
            prev[v] = 1;
        }

        for i in 1..n {
            let curr = nums[i] as usize;
            let delta = (nums[i] - nums[i - 1]).max(0) as usize; // d = max(0, nums[i]-nums[i-1])
            let mut cur = vec![0i64; max_val + 1];

            // j 表示当前 arr2[i] 的取值，范围是 [delta, curr]
            // 转移方程：dp[i][j] = sum_{t=0}^{j-delta} prev[t]
            // 利用前缀和优化：cur[j] = cur[j-1] + prev[j-delta]
            for j in delta..=curr {
                if j == delta {
                    cur[j] = prev[0];               // 此时 j-delta = 0
                } else {
                    cur[j] = (cur[j - 1] + prev[j - delta]) % MOD;
                }
            }
            // 对于 j 不在 [delta, curr] 范围内的值，cur[j] 保持为 0（符合转移要求）
            prev = cur;
        }

        // 答案：最后一行所有方案数之和
        prev.iter().fold(0, |acc, &x| (acc + x) % MOD) as i32
    }
}
```
