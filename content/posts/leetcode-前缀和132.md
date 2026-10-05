---
title: "leetcode-前缀和132"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计满足数位和数组的非递减数组数目

给你一个长度为 n 的整数数组 digitSum。

如果一个长度为 n 的数组 arr 满足以下条件，则认为它是 有效 的：

0 <= arr[i] <= 5000
它是 非递减 的。
arr[i] 的 数位和 等于 digitSum[i]。

返回一个整数，表示 不同的有效数组 的数量。由于答案可能很大，请将其对 10^9 + 7 取模后返回。

如果一个数组的每个元素都大于或等于它的前一个元素（如果存在），则称该数组是 非递减 的。


```
impl Solution {
    pub fn count_arrays(digit_sum: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        const MX: usize = 5001;
        const MAX_DS: usize = 31; // 4999 的最大数位和

        // 预处理所有数字的数位和
        // dig_sum[x] = x 的数位和，利用 dig_sum[x/10] 递推
        let mut dig_sum = [0u8; MX];
        for x in 1..MX {
            dig_sum[x] = dig_sum[x / 10] + (x % 10) as u8;
        }

        // sum[x] 表示满足约束且最后一个数 <= x 的方案数
        // 初始化为 1，代表任何数都可以作为第一个数
        let mut sum = vec![1i64; MX];
        let mut can_use = vec![false; MX];

        for &ds in &digit_sum {
            let ds = ds as usize;
            if ds > MAX_DS {
                return 0;
            }

            // 标记哪些数值在当前数位和约束下可用
            for x in 0..MX {
                can_use[x] = dig_sum[x] == ds as u8;
            }

            // 计算前缀和：f[x] = if dig_sum[x]==ds { sum[x] } else { 0 }
            // 原地更新 sum[x] 为 f[0..x] 的前缀和
            let mut running = 0i64;
            for x in 0..MX {
                if can_use[x] {
                    running = (running + sum[x]) % MOD;
                }
                // 注意：当 can_use[x] 为 false 时，f[x] = 0，但 running 保持不变
                // 这代表前缀和中不包含当前不可用的值
                sum[x] = running;
            }
        }

        sum[MX - 1] as i32
    }
}
```
