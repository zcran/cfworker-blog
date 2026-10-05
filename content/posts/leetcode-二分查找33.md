---
title: "leetcode-二分查找33"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 使数组严格递增

给你两个整数数组 arr1 和 arr2，返回使 arr1 严格递增所需要的最小「操作」数（可能为 0）。

每一步「操作」中，你可以分别从 arr1 和 arr2 中各选出一个索引，分别为 i 和 j，0 <= i < arr1.length 和 0 <= j < arr2.length，然后进行赋值运算 arr1[i] = arr2[j]。

如果无法让 arr1 严格递增，请返回 -1。


```
impl Solution {
    /// 返回使 arr1 严格递增所需的最小操作数，无法做到则返回 -1。
    ///
    /// 思路：动态规划 + 二分。
    ///   在 arr1 首尾分别插入哨兵 -1 和 INF，避免边界特判。
    ///   设 dp[i] 表示使 arr1[0..=i] 严格递增且 arr1[i] 未被替换（或替换后）的最小操作数，
    ///   但实际上这里 dp[i] 表示处理到原数组第 i 个位置（含哨兵偏移）的最小操作数。
    ///
    ///   转移：
    ///     1. 不替换 arr1[i]：若 arr1[i-1] < arr1[i]，则 dp[i] = min(dp[i], dp[i-1])。
    ///     2. 替换从 i-k 到 i-1 这段（共 k 个）为 arr2 中的连续 k 个数：
    ///        需满足 arr1[i-k-1] < arr2[j-k] 且 arr2 中这 k 个数递增（已排序去重保证），
    ///        则 dp[i] = min(dp[i], dp[i-k-1] + k)。
    ///
    ///   最终 dp[末尾哨兵] 即为答案（哨兵不参与替换）。
    pub fn make_array_increasing(mut arr1: Vec<i32>, mut arr2: Vec<i32>) -> i32 {
        const INF: i32 = 1_000_000_001;

        arr2.sort_unstable();
        arr2.dedup();

        // 首尾加哨兵，简化边界处理
        arr1.insert(0, -1);
        arr1.push(INF);

        let n = arr1.len();
        let mut dp = vec![INF; n];
        dp[0] = 0;

        for i in 1..n {
            // arr2 中小于 arr1[i] 的元素个数（可用于替换前 k 个位置）
            let j = arr2.partition_point(|&x| x < arr1[i]);

            // 枚举替换 arr1[i] 前面连续的 k 个元素
            for k in 1..=j.min(i - 1) {
                // 替换后需保证 arr1[i-k-1] < arr2[j-k]（arr2 已排序，后续自然递增）
                if arr1[i - k - 1] < arr2[j - k] {
                    dp[i] = dp[i].min(dp[i - k - 1] + k as i32);
                }
            }

            // 不替换 arr1[i]
            if arr1[i - 1] < arr1[i] {
                dp[i] = dp[i].min(dp[i - 1]);
            }
        }

        let res = dp[n - 1];
        if res >= INF { -1 } else { res }
    }
}
```
