---
title: "leetcode-位运算38"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 优质数对的数目

给你一个下标从 0 开始的正整数数组 nums 和一个正整数 k 。

如果满足下述条件，则数对 (num1, num2) 是 优质数对 ：

num1 和 num2 都 在数组 nums 中存在。
num1 OR num2 和 num1 AND num2 的二进制表示中值为 1 的位数之和大于等于 k ，其中 OR 是按位 或 操作，而 AND 是按位 与 操作。

返回 不同 优质数对的数目。

如果 a != c 或者 b != d ，则认为 (a, b) 和 (c, d) 是不同的两个数对。例如，(1, 2) 和 (2, 1) 不同。

注意：如果 num1 在数组中至少出现 一次 ，则满足 num1 == num2 的数对 (num1, num2) 也可以是优质数对。


```
impl Solution {
    /// 返回不同优质数对的数目。
    ///
    /// 关键性质：`popcount(num1 OR num2) + popcount(num1 AND num2)`
    ///           = `popcount(num1) + popcount(num2)`。
    /// 因此条件简化为两数的二进制 1 的个数之和不小于 k。
    ///
    /// 做法：先去重并统计每个数的 1 的个数，排序后对每个数二分查找
    /// 第一个满足和 ≥ k 的位置，累加满足条件的配对数量。
    pub fn count_excellent_pairs(nums: Vec<i32>, k: i32) -> i64 {
        // 去重并记录每个数的 1 的个数，然后按个数排序
        let mut counts: Vec<u32> = nums
            .into_iter()
            .collect::<std::collections::HashSet<_>>()
            .into_iter()
            .map(|v| v.count_ones())
            .collect();
        counts.sort_unstable();

        let n = counts.len();
        let k = k as u32;
        let mut ans = 0i64;

        for (i, &c) in counts.iter().enumerate() {
            // 找到第一个满足 c + counts[j] >= k 的下标 j
            let j = counts.partition_point(|&x| c + x < k);
            ans += (n - j) as i64;
        }

        ans
    }
}
```
