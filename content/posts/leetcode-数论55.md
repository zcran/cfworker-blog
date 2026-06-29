---
title: "leetcode-数论55"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最大子数组 GCD 分数

给你一个正整数数组 nums 和一个整数 k。

你最多可以执行 k 次操作。在每次操作中，你可以选择数组中的一个元素并将其值 翻倍 。每个元素 最多 只能翻倍一次。

连续 子数组 的 分数 定义为其所有元素的最大公约数 (GCD) 与子数组长度的 乘积 。

你的任务是返回修改后数组中选择一个连续子数组可以获得的最大 分数 。

注意：

子数组 是数组中连续的元素序列。

数组的 最大公约数 (GCD) 是能整除数组所有元素的最大整数。


```
impl Solution {
    pub fn max_gcd_score(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;
        let mut ans = 0i64;

        // 枚举所有子数组的右端点
        for right in 0..n {
            let mut gcd_val = 0;
            let mut min_lowbit = i32::MAX;
            let mut min_lowbit_count = 0;

            // 从右端点向左扩展
            for left in (0..=right).rev() {
                let x = nums[left];
                let lowbit = x & -x; // 获取最低位的 1

                // 更新最低位 1 的最小值和出现次数
                if lowbit < min_lowbit {
                    min_lowbit = lowbit;
                    min_lowbit_count = 1;
                } else if lowbit == min_lowbit {
                    min_lowbit_count += 1;
                }

                // 更新当前子数组的 GCD
                gcd_val = Self::gcd(gcd_val, x);

                // 如果最低位 1 出现次数 <= k，可以将 GCD 翻倍
                // 因为翻倍等价于在二进制左移一位，对应最低位 1 的变化
                let length = (right - left + 1) as i64;
                let score = if min_lowbit_count <= k {
                    gcd_val * 2
                } else {
                    gcd_val
                };

                ans = ans.max(score as i64 * length);
            }
        }

        ans
    }

    /// 计算最大公约数
    #[inline]
    fn gcd(mut a: i32, mut b: i32) -> i32 {
        while b != 0 {
            let temp = a % b;
            a = b;
            b = temp;
        }
        a.abs()
    }
}
```
