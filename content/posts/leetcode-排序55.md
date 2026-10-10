---
title: "leetcode-排序55"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 统计将重叠区间合并成组的方案数

给你一个二维整数数组 ranges ，其中 ranges[i] = [starti, endi] 表示 starti 到 endi 之间（包括二者）的所有整数都包含在第 i 个区间中。

你需要将 ranges 分成 两个 组（可以为空），满足：

· 每个区间只属于一个组。
· 两个有 交集 的区间必须在 同一个 组内。

如果两个区间有至少 一个 公共整数，那么这两个区间是 有交集 的。

· 比方说，区间 [1, 3] 和 [2, 5] 有交集，因为 2 和 3 在两个区间中都被包含。

请你返回将 ranges 划分成两个组的 总方案数 。由于答案可能很大，将它对 10^9 + 7 取余 后返回。




```
impl Solution {
    /// 返回将 ranges 分成两个组的总方案数，对 1e9+7 取余。
    ///
    /// 思路：
    /// 1. 按区间起点升序排序，合并所有有交集的区间，得到若干个互不相交的连通块。
    /// 2. 每个连通块（整块区间）必须整体分到同一个组，因此每个连通块有 2 种选择（组 A 或组 B）。
    /// 3. 若有 k 个连通块，方案数即为 2^k，再对 1e9+7 取余。
    ///
    /// 注意：最后乘 2 时漏掉了空组之外的情况——两个组可以为空，
    /// 所以每个连通块两种归属都可选，答案就是 2^k。
    pub fn count_ways(mut ranges: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        ranges.sort_unstable_by_key(|r| r[0]);

        let mut ways: i64 = 1;
        let n = ranges.len();
        let mut i = 0;

        while i < n {
            // 当前连通块的右端点
            let mut right = ranges[i][1];
            let mut j = i + 1;

            // 合并所有与当前块有交集的区间
            while j < n && ranges[j][0] <= right {
                right = right.max(ranges[j][1]);
                j += 1;
            }

            // 每个连通块可任选分到组 A 或组 B
            ways = ways * 2 % MOD;
            i = j;
        }

        ways as i32
    }
}
```
