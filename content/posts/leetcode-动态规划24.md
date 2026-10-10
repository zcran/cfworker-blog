---
title: "leetcode-动态规划24"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 等差数列划分 II - 子序列

给你一个整数数组 nums ，返回 nums 中所有 等差子序列 的数目。

如果一个序列中 至少有三个元素 ，并且任意两个相邻元素之差相同，则称该序列为等差序列。

· 例如，[1, 3, 5, 7, 9]、[7, 7, 7, 7] 和 [3, -1, -5, -9] 都是等差序列。
· 再例如，[1, 1, 2, 5, 7] 不是等差序列。

数组中的子序列是从数组中删除一些元素（也可能不删除）得到的一个序列。

· 例如，[2,5,10] 是 [1,2,1,2,4,1,5,10] 的一个子序列。

题目数据保证答案是一个 32-bit 整数。


```
use std::collections::HashMap;

impl Solution {
    pub fn number_of_arithmetic_slices(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        // dp[i][d]：以 nums[i] 结尾、公差为 d 的等差子序列个数（长度 >= 2）
        let mut dp: Vec<HashMap<i64, i32>> = vec![HashMap::new(); n];
        let mut ans = 0;

        for i in 1..n {
            for j in 0..i {
                let d = nums[i] as i64 - nums[j] as i64;
                // 以 j 结尾、公差为 d 的子序列数（长度 >= 2）
                let prev = dp[j].get(&d).copied().unwrap_or(0);
                // 这些子序列接上 nums[i] 后长度 >= 3，都是新的等差子序列
                ans += prev;
                // nums[j] 和 nums[i] 本身构成一个长度 2 的子序列
                *dp[i].entry(d).or_insert(0) += prev + 1;
            }
        }

        ans
    }
}
```
