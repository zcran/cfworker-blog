---
title: "leetcode-动态规划138"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 查询子数组最大异或分数

给你一个由 n 个整数组成的数组 nums，以及一个大小为 q 的二维整数数组 queries，其中 queries[i] = [li, ri]。

对于每一个查询，你需要找出 nums[li..ri] 中任意 子数组 的 最大异或分数。

数组的异或分数 需要对数组 a 反复执行以下操作，直到只剩一个元素，剩下的那个元素就是 异或分数：

对于除最后一个下标以外的所有下标 i，同时将 a[i] 替换为 a[i] XOR a[i + 1] 。
移除数组的最后一个元素。

返回一个大小为 q 的数组 answer，其中 answer[i] 表示查询 i 的答案。




```
impl Solution {
    pub fn maximum_subarray_xor(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = nums.len();
        let mut f = nums;

        // mx[i][j] = f[i..=j] 内所有子数组的最大异或分数
        let mut mx = vec![vec![0i32; n]; n];

        // 从右往左、从下往上递推
        for i in (0..n).rev() {
            mx[i][i] = f[i];
            for j in i + 1..n {
                // 原地更新：f[j] 变为 nums[i..=j] 的异或分数
                f[j] ^= f[j - 1];
                mx[i][j] = f[j].max(mx[i + 1][j]).max(mx[i][j - 1]);
            }
        }

        queries
            .iter()
            .map(|q| mx[q[0] as usize][q[1] as usize])
            .collect()
    }
}
```
