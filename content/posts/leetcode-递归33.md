---
title: "leetcode-递归33"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 不同三位偶数的数目

给你一个数字数组 digits，你需要从中选择三个数字组成一个三位偶数，你的任务是求出 不同 三位偶数的数量。

注意：每个数字在三位偶数中都只能使用 一次 ，并且 不能 有前导零。



```
use std::collections::HashSet;

impl Solution {
    /// 计算所有由给定数字组成的三位偶数的个数
    ///
    /// # 算法思路
    /// 使用计数数组统计每个数字的出现次数，然后枚举百位、十位、个位的可能取值，
    /// 这样可以将时间复杂度从 O(n³) 优化到 O(10³) = O(1)，与输入数组长度无关。
    ///
    /// # 参数
    /// - `digits`: 可用的数字数组（可能包含重复）
    ///
    /// # 返回
    /// - 能够组成的三位偶数的个数
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::total_numbers(vec![1, 2, 3, 4]), 12);
    /// assert_eq!(Solution::total_numbers(vec![0, 2, 2]), 2);
    /// ```
    pub fn total_numbers(digits: Vec<i32>) -> i32 {
        // 统计每个数字（0-9）的出现次数
        let mut count = [0; 10];
        for &d in &digits {
            count[d as usize] += 1;
        }

        let mut result = HashSet::new();

        // 枚举百位数（不能为0）
        for hundreds in 1..=9 {
            if count[hundreds as usize] == 0 {
                continue;
            }
            count[hundreds as usize] -= 1;  // 使用一个百位数

            // 枚举十位数（可以为0）
            for tens in 0..=9 {
                if count[tens as usize] == 0 {
                    continue;
                }
                count[tens as usize] -= 1;  // 使用一个十位数

                // 枚举个位数（必须为偶数）
                for units in (0..=8).step_by(2) {
                    if count[units as usize] > 0 {
                        let num = hundreds * 100 + tens * 10 + units;
                        result.insert(num);
                    }
                }

                count[tens as usize] += 1;  // 恢复十位数
            }

            count[hundreds as usize] += 1;  // 恢复百位数
        }

        result.len() as i32
    }
}
```
