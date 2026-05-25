---
title: "leetcode-记忆化32"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 相同分数的最大操作数目 II

给你一个整数数组 nums ，如果 nums 至少 包含 2 个元素，你可以执行以下操作中的 任意 一个：

选择 nums 中最前面两个元素并且删除它们。
选择 nums 中最后两个元素并且删除它们。
选择 nums 中第一个和最后一个元素并且删除它们。

一次操作的 分数 是被删除元素的和。

在确保 所有操作分数相同 的前提下，请你求出 最多 能进行多少次操作。

请你返回按照上述要求 最多 可以进行的操作次数。

```
impl Solution {
    /// 返回最大操作次数，每次移除两个和为固定目标值的元素。
    /// 目标值由第一次移除的两数之和决定，可行移除方式：前两个、后两个、首尾各一。
    pub fn max_operations(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        // 长度小于 2 无法操作
        if n < 2 {
            return 0;
        }
        // 长度恰好为 2 时只能进行一次操作
        if n == 2 {
            return 1;
        }

        // memo[i][j] 存储子数组 nums[i..=j] 的最大操作次数，-1 表示未计算
        let mut memo = vec![vec![-1; n]; n];

        // 深度优先搜索（记忆化递归）
        fn dfs(i: usize, j: usize, t: i32, nums: &[i32], memo: &mut Vec<Vec<i32>>) -> i32 {
            // 无效区间，无法继续操作
            if i >= j {
                return 0;
            }
            // 查表
            if memo[i][j] != -1 {
                return memo[i][j];
            }

            let n = nums.len();
            // 三种移除方式的最大操作数（加上本次操作 1）
            let ans = [
                // 1. 移除前两个元素：需要 i+1 < n 且 i+2 <= j
                if i + 1 < n && i + 2 <= j && nums[i] + nums[i + 1] == t {
                    dfs(i + 2, j, t, nums, memo) + 1
                } else {
                    0
                },
                // 2. 移除后两个元素：需要 j >= 2 且 i <= j-2
                if j >= 2 && i <= j - 2 && nums[j] + nums[j - 1] == t {
                    dfs(i, j - 2, t, nums, memo) + 1
                } else {
                    0
                },
                // 3. 移除首尾元素：需要 i < j（已经由上层保证）且 i+1 <= j-1 自动满足
                if nums[i] + nums[j] == t {
                    dfs(i + 1, j - 1, t, nums, memo) + 1
                } else {
                    0
                },
            ]
            .iter()
            .max()
            .copied()
            .unwrap_or(0);

            memo[i][j] = ans;
            ans
        }

        // 三种可能的首次操作对应的目标值及剩余区间
        let t1 = nums[0] + nums[1];               // 移除前两个
        let t2 = nums[0] + nums[n - 1];           // 移除首尾
        let t3 = nums[n - 1] + nums[n - 2];       // 移除后两个

        let a = dfs(2, n - 1, t1, &nums, &mut memo);
        let b = dfs(1, n - 2, t2, &nums, &mut memo);
        let c = dfs(0, n - 3, t3, &nums, &mut memo);

        // 加上首次操作
        a.max(b.max(c)) + 1
    }
}
```
