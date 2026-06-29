---
title: "leetcode-数论67"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 可整除替换后的数组最小元素和

给你一个整数数组 nums。

你可以执行以下操作任意多次：

· 选择两个下标 a 和 b，且满足 nums[a] % nums[b] == 0。
· 将 nums[a] 替换为 nums[b]。

返回执行任意次操作后，数组可能得到的 最小 元素和。


```
impl Solution {
    /// 返回执行操作后数组可能得到的最小元素和
    ///
    /// 操作：如果 nums[a] % nums[b] == 0，可将 nums[a] 替换为 nums[b]
    /// 最优策略：将每个数替换为它的最小因子（该因子也在数组中）
    pub fn min_array_sum(mut nums: Vec<i32>) -> i64 {
        if nums.is_empty() {
            return 0;
        }

        // 排序以便统计和找最大值
        nums.sort_unstable();
        let max_val = nums[nums.len() - 1] as usize;

        // 记录每个数的最小因子（-1 表示未计算）
        let mut min_factor = vec![-1i32; max_val + 1];
        let mut ans = 0i64;

        // 使用筛法为所有出现过的数计算最小因子
        // 外层遍历：每个不同的数字作为潜在因子
        let mut i = 0;
        while i < nums.len() {
            let n = nums[i] as usize;

            // 跳过重复数字
            let freq = {
                let mut j = i + 1;
                while j < nums.len() && nums[j] as usize == n {
                    j += 1;
                }
                j - i
            };

            // 如果 n 的最小因子还未计算
            if min_factor[n] == -1 {
                // 使用筛法：n 是它的所有倍数的最小因子
                for multiple in (n..=max_val).step_by(n) {
                    if min_factor[multiple] == -1 {
                        min_factor[multiple] = n as i32;
                    }
                }
            }

            // 累加答案（所有相同的数都替换为最小因子）
            ans += (min_factor[n] as i64) * (freq as i64);

            i += freq;
        }

        ans
    }
}
```
