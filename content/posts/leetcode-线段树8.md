---
title: "leetcode-线段树8"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 删除一个冲突对后最大子数组数目

给你一个整数 n，表示一个包含从 1 到 n 按顺序排列的整数数组 nums。此外，给你一个二维数组 conflictingPairs，其中 conflictingPairs[i] = [a, b] 表示 a 和 b 形成一个冲突对。

从 conflictingPairs 中删除 恰好 一个元素。然后，计算数组 nums 中的非空子数组数量，这些子数组都不能同时包含任何剩余冲突对 [a, b] 中的 a 和 b。

返回删除 恰好 一个冲突对后可能得到的 最大 子数组数量。

子数组 是数组中一个连续的 非空 元素序列。


```
impl Solution {
    /// 计算删除恰好一个冲突对后，不包含任何剩余冲突对的子数组最大数量
    ///
    /// 思路：对于每个左端点 i，找到最小的右端点 min_r[i]，使得子数组 [i..min_r[i]] 包含冲突对。
    /// 那么以 i 为左端点的合法子数组数量 = min_r[i] - i。
    ///
    /// 维护两个最小的 b 值（b_min1 和 b_min2），用于计算删除某个冲突对后的影响。
    ///
    /// 时间复杂度 O(n + m)，空间复杂度 O(n)
    pub fn max_subarrays(n: i32, conflicting_pairs: Vec<Vec<i32>>) -> i64 {
        let n = n as usize;
        // b_min1[i]: 以 i 为左端点的最小右端点（冲突对的右端点）
        // b_min2[i]: 以 i 为左端点的次小右端点
        let mut b_min1 = vec![n + 1; n + 1]; // 用 n+1 表示无穷大
        let mut b_min2 = vec![n + 1; n + 1];

        // 预处理每个左端点对应的最小和次小右端点
        for pair in &conflicting_pairs {
            let a = pair[0].min(pair[1]) as usize;
            let b = pair[0].max(pair[1]) as usize;
            if b < b_min1[a] {
                b_min2[a] = b_min1[a];
                b_min1[a] = b;
            } else if b < b_min2[a] {
                b_min2[a] = b;
            }
        }

        let mut total = 0i64;
        let mut best_l = n; // 初始为最后一个有效索引
        let mut sec_min = n + 1; // 第二小的右端点
        let mut improvement = vec![0i64; n + 1]; // 删除每个冲突对能增加的子数组数量

        // 从右向左遍历每个左端点 i
        for i in (1..=n).rev() {
            // 更新 best_l：选择 b_min1 最小的左端点
            if b_min1[best_l] > b_min1[i] {
                sec_min = sec_min.min(b_min1[best_l]);
                best_l = i;
            } else {
                sec_min = sec_min.min(b_min1[i]);
            }

            // 以 i 为左端点的合法子数组数量
            let first_min = b_min1[best_l].min(n + 1);
            total += (first_min - i) as i64;

            // 计算删除 best_l 对应的冲突对后增加的子数组数量
            let new_bound = sec_min.min(b_min2[best_l]).min(n + 1);
            let old_bound = b_min1[best_l].min(n + 1);
            if new_bound > old_bound {
                improvement[best_l] += (new_bound - old_bound) as i64;
            }
        }

        // 选择删除一个冲突对，使得增加的子数组数量最大
        total + *improvement.iter().max().unwrap()
    }
}
```
