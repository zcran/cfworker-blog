---
title: "leetcode-位掩码3"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


##  数组的均值分割

给定你一个整数数组 nums

我们要将 nums 数组中的每个元素移动到 A 数组 或者 B 数组中，使得 A 数组和 B 数组不为空，并且 average(A) == average(B) 。

如果可以完成则返回true ， 否则返回 false  。

注意：对于数组 arr ,  average(arr) 是 arr 的所有元素的和除以 arr 长度。

```
impl Solution {
    pub fn split_array_same_average(nums: Vec<i32>) -> bool {
        let n = nums.len();
        if n == 1 {
            return false; // 无法分成两个非空子集
        }
        let sum: i32 = nums.iter().sum();
        // 使用 u32 作为位掩码，每个 bit 表示子集大小（最多 30 个数，u32 足够）
        let mut dp = vec![0u32; (sum + 1) as usize];
        dp[0] = 1; // 和为 0 的子集大小为 0（第 0 位为 1）

        for &num in &nums {
            // 从大到小更新，避免重复使用同一个数
            for s in (num..=sum).rev() {
                let prev = dp[(s - num) as usize];
                if prev != 0 {
                    dp[s as usize] |= prev << 1;
                }
            }
        }

        // 枚举可能的子集大小 i（1..n），检查是否存在和为 s 且大小为 i 的子集
        for i in 1..n {
            if (sum * i as i32) % (n as i32) != 0 {
                continue;
            }
            let target_sum = (sum * i as i32) / (n as i32);
            let mask = dp[target_sum as usize];
            if mask != 0 && (mask >> i) & 1 == 1 {
                return true;
            }
        }
        false
    }
}
```
