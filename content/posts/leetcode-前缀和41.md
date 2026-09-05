---
title: "leetcode-前缀和41"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 装包裹的最小浪费空间

给你 n 个包裹，你需要把它们装在箱子里，每个箱子装一个包裹。总共有 m 个供应商提供 不同尺寸 的箱子（每个规格都有无数个箱子）。如果一个包裹的尺寸 小于等于 一个箱子的尺寸，那么这个包裹就可以放入这个箱子之中。

包裹的尺寸用一个整数数组 packages 表示，其中 packages[i] 是第 i 个包裹的尺寸。供应商用二维数组 boxes 表示，其中 boxes[j] 是第 j 个供应商提供的所有箱子尺寸的数组。

你想要选择 一个供应商 并只使用该供应商提供的箱子，使得 总浪费空间最小 。对于每个装了包裹的箱子，我们定义 浪费的 空间等于 箱子的尺寸 - 包裹的尺寸 。总浪费空间 为 所有 箱子中浪费空间的总和。

比方说，如果你想要用尺寸数组为 [4,8] 的箱子装下尺寸为 [2,3,5] 的包裹，你可以将尺寸为 2 和 3 的两个包裹装入两个尺寸为 4 的箱子中，同时把尺寸为 5 的包裹装入尺寸为 8 的箱子中。总浪费空间为 (4-2) + (4-3) + (8-5) = 6 。

请你选择 最优 箱子供应商，使得 总浪费空间最小 。如果 无法 将所有包裹放入箱子中，请你返回 -1 。由于答案可能会 很大 ，请返回它对 10^9 + 7 取余 的结果。


```
impl Solution {
    /// 计算选择最优供应商时的最小总浪费空间
    ///
    /// 思路：
    /// 1. 将包裹升序排列，计算前缀和以便快速计算区间和
    /// 2. 对每个供应商的箱子升序排列
    /// 3. 用箱子依次包裹包裹：每个箱子能装下当前及之前所有未装箱的包裹
    /// 4. 浪费空间 = 箱子尺寸 × 装入包裹数 - 这些包裹的总尺寸
    pub fn min_wasted_space(packages: Vec<i32>, boxes: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let mut packages = packages;
        packages.sort_unstable();
        let n = packages.len();

        // 前缀和，pref[i] 表示前 i 个包裹的总尺寸
        let mut pref = vec![0i64; n + 1];
        for (i, &p) in packages.iter().enumerate() {
            pref[i + 1] = pref[i] + p as i64;
        }

        // 闭包：计算包裹区间 [l, r] 的总尺寸
        let sum_range = |l: usize, r: usize| -> i64 {
            if l > r { 0 } else { pref[r + 1] - pref[l] }
        };

        let mut min_waste = i64::MAX;

        for mut box_sizes in boxes {
            box_sizes.sort_unstable();

            // 如果最大包裹放不进最大箱子，跳过该供应商
            if packages[n - 1] > box_sizes[box_sizes.len() - 1] {
                continue;
            }

            let mut waste = 0i64;
            let mut pos = 0; // 当前已装箱的包裹数量

            for &box_size in &box_sizes {
                if box_size < packages[pos] {
                    continue;
                }

                // 找到能装入当前箱子的最大包裹索引
                let next_pos = packages[pos..].partition_point(|&p| p <= box_size) + pos;
                let count = next_pos - pos;

                // 当前箱子的浪费空间 = count * box_size - 这些包裹的总尺寸
                waste += count as i64 * box_size as i64 - sum_range(pos, next_pos - 1);
                pos = next_pos;

                if pos == n {
                    break;
                }
            }

            if pos == n {
                min_waste = min_waste.min(waste);
            }
        }

        if min_waste == i64::MAX { -1 } else { (min_waste % MOD) as i32 }
    }
}
```
