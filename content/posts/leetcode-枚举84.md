---
title: "leetcode-枚举84"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 划分数组得到最大异或运算和与运算之和

给你一个整数数组 nums。

将数组划分为 三 个（可以为空）子序列 A、B 和 C，使得 nums 中的每个元素 恰好 属于一个子序列。

你的目标是 最大化 以下值：XOR(A) + AND(B) + XOR(C)

其中：

XOR(arr) 表示 arr 中所有元素的按位异或结果。如果 arr 为空，结果定义为 0。

AND(arr) 表示 arr 中所有元素的按位与结果。如果 arr 为空，结果定义为 0。

返回可实现的最 大 值。

注意: 如果有多种划分方式得到相同的 最大 和，你可以按其中任何一种划分。

子序列 是指一个数组通过删除一些或不删除任何元素，不改变剩余元素的顺序得到的元素序列。


```
impl Solution {
    pub fn maximize_xor_and_xor(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        let total_masks = 1 << n;

        // 预处理所有子集的 AND、XOR 和 OR 值
        let mut and = vec![0i64; total_masks];
        let mut xor = vec![0i64; total_masks];
        let mut or = vec![0i64; total_masks];

        // 空集的 AND 需要特殊处理（全 1 掩码）
        and[0] = -1;

        for (idx, &num) in nums.iter().enumerate() {
            let bit = 1 << idx;
            let val = num as i64;
            for mask in 0..bit {
                let new_mask = bit | mask;
                and[new_mask] = and[mask] & val;
                xor[new_mask] = xor[mask] ^ val;
                or[new_mask] = or[mask] | val;
            }
        }
        and[0] = 0; // 空集的 AND 定义为 0

        // 构建线性基用于计算最大异或
        struct XorBasis {
            basis: Vec<u32>,
        }

        impl XorBasis {
            fn new(max_bits: usize) -> Self {
                Self { basis: vec![0; max_bits] }
            }

            fn insert(&mut self, mut x: u32) {
                while x > 0 {
                    let pos = (32 - x.leading_zeros() - 1) as usize;
                    if self.basis[pos] == 0 {
                        self.basis[pos] = x;
                        return;
                    }
                    x ^= self.basis[pos];
                }
            }

            fn max_xor(&self) -> u32 {
                let mut result = 0;
                for &b in self.basis.iter().rev() {
                    if (result ^ b) > result {
                        result ^= b;
                    }
                }
                result
            }
        }

        // 计算给定子集的最大 XOR 值（限制在可用元素范围内）
        let max_xor_for_subset = |subset_mask: i32, max_bits: usize, xor_arr: &[i64]| -> i64 {
            let mut basis = XorBasis::new(max_bits);
            let fixed_xor = xor_arr[subset_mask as usize];

            for (i, &num) in nums.iter().enumerate() {
                if (subset_mask >> i) & 1 == 1 {
                    // 移除固定 XOR 的影响，只保留自由位
                    let free_part = (num as i64 & !fixed_xor) as u32;
                    basis.insert(free_part);
                }
            }

            fixed_xor + 2 * basis.max_xor() as i64
        };

        let max_bits = 64 - nums.iter().max().unwrap().leading_zeros() as usize;
        let mut answer = 0i64;

        // 枚举 A 子集（AND 部分）
        // B 和 C 是互补子集（由剩余元素划分）
        for a_mask in 0..total_masks {
            let b_and_c_mask = (total_masks - 1) ^ a_mask;
            let and_a = and[a_mask];

            // 计算 B 和 C 的分配使得 XOR(B) + XOR(C) 最大化
            // 对于给定的 B∪C 集合（即剩余元素集合），最大异或和是：
            // max_xor(B) + max_xor(C) = max_xor(B∪C) + max_xor(B∩C的自由位)
            // 但由于 B 和 C 可以任意划分，最大值为 max_xor(B∪C) * 2
            let current = and_a + max_xor_for_subset(
                b_and_c_mask as i32,
                max_bits,
                &xor
            );

            answer = answer.max(current);
        }

        answer
    }
}
```
