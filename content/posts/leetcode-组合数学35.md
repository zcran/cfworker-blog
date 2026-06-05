---
title: "leetcode-组合数学35"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 唯一中间众数子序列 I

给你一个整数数组 nums ，请你求出 nums 中大小为 5 的 子序列 的数目，它是 唯一中间众数序列 。

由于答案可能很大，请你将答案对 10^9 + 7 取余 后返回。

众数 指的是一个数字序列中出现次数 最多 的元素。

如果一个数字序列众数只有一个，我们称这个序列有 唯一众数 。

一个大小为 5 的数字序列 seq ，如果它中间的数字（seq[2]）是唯一众数，那么称它是 唯一中间众数 序列。

```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn subsequences_with_middle_mode(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n < 5 {
            return 0;
        }

        // 辅助函数：组合数 C(x, 2)
        let comb2 = |x: i64| if x < 2 { 0 } else { x * (x - 1) / 2 % MOD };

        // 所有不同的数值（用于遍历 y）
        let mut all_vals = nums.clone();
        all_vals.sort();
        all_vals.dedup();

        // 总方案数 C(n, 5)
        let total = {
            let n = n as i128;
            let t = n * (n - 1) * (n - 2) * (n - 3) * (n - 4) / 120;
            (t % MOD as i128) as i64
        };
        let mut ans = total;

        use std::collections::HashMap;
        let mut pre = HashMap::new(); // 前缀计数
        let mut suf = HashMap::new(); // 后缀计数
        for &x in &nums {
            *suf.entry(x).or_insert(0) += 1;
        }

        // 枚举中间元素的位置 left（子序列中第 3 个元素在原数组中的下标）
        for left in 0..n - 2 {
            let x = nums[left];
            // 当前元素从后缀移动到前缀
            *suf.get_mut(&x).unwrap() -= 1; // 计数减 1，但保留键（值为 0 也不删除）

            if left >= 2 {
                let right = (n - 1 - left) as i64; // 右边剩余元素个数
                let pre_x = *pre.get(&x).unwrap_or(&0) as i64;
                let suf_x = *suf.get(&x).unwrap_or(&0) as i64;
                let left_others = left as i64 - pre_x;      // 左边非 x 的元素个数
                let right_others = right - suf_x;           // 右边非 x 的元素个数

                // 1. 只有一个 x（非法）：左右各选两个非 x
                let term1 = comb2(left_others) * comb2(right_others) % MOD;
                ans = (ans - term1 + MOD) % MOD;

                // 遍历所有不同的数值 y（y != x）
                for &y in &all_vals {
                    if y == x {
                        continue;
                    }
                    let pre_y = *pre.get(&y).unwrap_or(&0) as i64;
                    let suf_y = *suf.get(&y).unwrap_or(&0) as i64;

                    // 安全计算，避免负数（逻辑上应为非负，此处取 max 防御）
                    let right_others_minus_suf_y = (right - suf_x - suf_y).max(0);
                    let left_others_minus_pre_y = (left as i64 - pre_x - pre_y).max(0);

                    // 2. 左边两个 y，右边一个 x（两个 x 的情况，y 出现 2 次）
                    let term2 = comb2(pre_y) * (suf_x % MOD) % MOD * (right_others % MOD) % MOD;
                    ans = (ans - term2 + MOD) % MOD;

                    // 3. 右边两个 y，左边一个 x（两个 x 的情况，y 出现 2 次）
                    let term3 = comb2(suf_y) * (pre_x % MOD) % MOD * (left_others % MOD) % MOD;
                    ans = (ans - term3 + MOD) % MOD;

                    // 4. 左右各一个 y，另一个 x 在左边（两个 x 和两个 y，剩余一个 z）
                    let term4 = (pre_y % MOD) * (suf_y % MOD) % MOD
                        * (pre_x % MOD) % MOD
                        * (right_others_minus_suf_y % MOD) % MOD;
                    ans = (ans - term4 + MOD) % MOD;

                    // 5. 左右各一个 y，另一个 x 在右边（两个 x 和两个 y，剩余一个 z）
                    let term5 = (pre_y % MOD) * (suf_y % MOD) % MOD
                        * (suf_x % MOD) % MOD
                        * (left_others_minus_pre_y % MOD) % MOD;
                    ans = (ans - term5 + MOD) % MOD;
                }
            }

            // 将当前元素加入前缀
            *pre.entry(x).or_insert(0) += 1;
        }

        ans as i32
    }
}
```
