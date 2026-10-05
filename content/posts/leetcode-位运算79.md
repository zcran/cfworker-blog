---
title: "leetcode-位运算79"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 增加操作后最大按位与的结果

给你一个整数数组 nums 和两个整数 k 与 m。

你 最多 可以执行 k 次操作。在每次操作中，你可以选择任意下标 i 并将 nums[i] 增加 1。

返回在执行最多 k 次操作后，任意大小为 m 的 子集 的 按位与 结果的 最大 可能值。

数组的 子集 是指从数组中选择的一组元素。


```
impl Solution {
    pub fn maximum_and(nums: Vec<i32>, k: i32, m: i32) -> i32 {
        let n = nums.len();
        let k = k as i64;
        let m = m as usize;

        // 每个数要凑到 target 所需的操作次数（复用缓冲区，避免重复分配）
        let mut ops = vec![0i64; n];

        let max_val = *nums.iter().max().unwrap();
        // 上界：任何单个数最多增大到 max(nums) + k/m
        let max_width = 32 - ((max_val + (k / m as i64) as i32) as u32).leading_zeros();

        let mut ans = 0i32;
        // 从高位到低位贪心：尝试把当前位设为 1，看能否凑够 m 个数
        for bit in (0..max_width).rev() {
            let target = ans | (1 << bit);

            // 计算每个 nums[i] 提升到包含 target 所有已确定 1 位所需的最小操作数
            for i in 0..n {
                let x = nums[i];
                // 找出 target 中为 1 但 x 中为 0 的最高位 j-1，只需把 x 的低 j 位提升到 target 的低 j 位
                let diff = (target & !x) as u32;
                let j = 32 - diff.leading_zeros();
                let mask = if j == 32 { u32::MAX as i32 } else { (1i32 << j) - 1 };
                ops[i] = ((target & mask) - (x & mask)) as i64;
            }

            // 取操作次数最小的 m 个数，若总和 <= k，则该位可行
            ops.select_nth_unstable(m - 1);
            let sum: i64 = ops[..m].iter().sum();
            if sum <= k {
                ans = target;
            }
        }
        ans
    }
}
```
