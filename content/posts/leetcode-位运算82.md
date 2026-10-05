---
title: "leetcode-位运算82"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 达到目标异或值的最少删除次数

给你一个整数数组 nums 和一个整数 target。

你可以从 nums 中移除 任意 数量的元素（可能为零）。

返回使剩余元素的 按位异或和 等于 target 所需的 最小 移除次数。如果无法达到 target，则返回 -1。

空数组的按位异或和为 0。


```
impl Solution {
    pub fn min_removals(nums: Vec<i32>, target: i32) -> i32 {
        // 问题等价于：从 nums 中选出一个子集，使其异或和恰为 target，
        // 且子集大小最大。答案 = n - 最大子集大小。
        //
        // 用 DP：f[mask] = 能得到异或值 mask 的最大子集大小（-inf 表示不可达）。
        // 值域上界取 2^m，其中 m 是 nums 最大值的二进制位数。

        let max_val = *nums.iter().max().unwrap();
        let m = 32 - max_val.leading_zeros(); // nums 元素的最大位数
        let size = 1usize << m;

        // 若 target 超出可达值域，直接无解
        if target as u32 >= size as u32 {
            return -1;
        }

        const NEG: i32 = i32::MIN / 2;
        let mut f = vec![NEG; size];
        f[0] = 0;

        let mut nf = vec![NEG; size];
        for &x in &nums {
            for j in 0..size {
                // 不选 x：f[j]；选 x：f[j ^ x] + 1
                nf[j] = f[j].max(f[j ^ x as usize] + 1);
            }
            std::mem::swap(&mut f, &mut nf);
        }

        if f[target as usize] < 0 {
            return -1;
        }
        nums.len() as i32 - f[target as usize]
    }
}
```
