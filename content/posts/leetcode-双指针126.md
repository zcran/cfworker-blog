---
title: "leetcode-双指针126"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 合并有序列表的最小成本

给你一个二维整数数组 lists，其中每个 lists[i] 是一个按照 非递减顺序 排序的非空整数数组。

你可以 重复 选择两个列表 a = lists[i] 和 b = lists[j]（i != j），并将它们合并。合并 a 和 b 的 成本 为：

len(a) + len(b) + abs(median(a) - median(b))，其中 len 和 median 分别表示列表的长度和中位数。

合并 a 和 b 后，从 lists 中移除 a 和 b，并将新的合并后 有序列表（元素按从小到大排列）插入到 lists 中的 任意 位置。重复此过程直到只剩下 一个 列表。

返回将所有列表合并为一个有序列表所需的 最小总成本。

数组的 中位数 是指排序后位于中间的元素。如果数组元素数量为偶数，则取左侧中间元素。


```
impl Solution {
    pub fn min_merge_cost(rivers: Vec<Vec<i32>>) -> i64 {
        let n = rivers.len();
        let full = 1usize << n; // 状态总数

        // cost[mask]：合并 mask 中所有列表的总长度（合并成本中的 len 部分之和）
        // med[mask] ：合并 mask 中所有列表后的中位数
        let mut cost = vec![0i64; full];
        let mut med = vec![0i64; full];

        // 预处理每个非空子集
        for mask in 1..full {
            // 收集该子集内的所有元素
            let mut waters: Vec<i64> = Vec::new();
            for i in 0..n {
                if mask >> i & 1 == 1 {
                    waters.extend(rivers[i].iter().map(|&v| v as i64));
                }
            }

            cost[mask] = waters.len() as i64;

            // 中位数：偶数长度取左侧中间元素
            let center = (waters.len() - 1) / 2;
            waters.select_nth_unstable(center);
            med[mask] = waters[center];
        }

        // dp[mask]：把 mask 中所有列表合并为一个的最小总成本
        let mut dp = vec![i64::MAX / 2; full];
        dp[0] = 0;
        for i in 0..n {
            dp[1 << i] = 0; // 单个列表无需合并
        }

        // 按子集大小 / 数值顺序枚举，保证子状态先被计算
        for mask in 1..full {
            // 枚举 mask 的所有非空真子集 sub，用 sub 与 mask^sub 合并
            let mut sub = (mask - 1) & mask;
            while sub > 0 {
                let other = mask ^ sub;
                if other != 0 {
                    let merge_cost = cost[sub]
                        + cost[other]
                        + (med[sub] - med[other]).abs();
                    let candidate = dp[sub] + dp[other] + merge_cost;
                    if candidate < dp[mask] {
                        dp[mask] = candidate;
                    }
                }
                sub = (sub - 1) & mask;
            }
        }

        dp[full - 1]
    }
}
```
