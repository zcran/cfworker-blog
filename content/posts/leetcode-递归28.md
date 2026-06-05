---
title: "leetcode-递归28"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 找出 3 位偶数

给你一个整数数组 digits ，其中每个元素是一个数字（0 - 9）。数组中可能存在重复元素。

你需要找出 所有 满足下述条件且 互不相同 的整数：

该整数由 digits 中的三个元素按 任意 顺序 依次连接 组成。
该整数不含 前导零
该整数是一个 偶数

例如，给定的 digits 是 [1, 2, 3] ，整数 132 和 312 满足上面列出的全部条件。

将找出的所有互不相同的整数按 递增顺序 排列，并以数组形式返回。


```
use std::collections::HashSet;

impl Solution {
    /// 找出所有由给定数字组成的三位偶数
    ///
    /// # 问题说明
    /// 给定一个数字数组（可能包含重复），找出所有由其中三个不同位置的数字
    /// 组成的三位偶数（100-998，且个位为偶数）
    ///
    /// # 算法优化
    /// 1. 使用计数数组代替 HashSet 减少内存分配
    /// 2. 直接遍历所有可能的百位、十位、个位数字组合
    /// 3. 提前剪枝减少无效循环
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n^3) 最坏情况，但通过计数优化实际更快
    /// - 空间复杂度：O(10) = O(1)，固定大小的计数数组
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::find_even_numbers(vec![2,1,3,0]), vec![102,120,130,132,210,230,302,310,312,320]);
    /// ```
    pub fn find_even_numbers(digits: Vec<i32>) -> Vec<i32> {
        // 使用计数数组统计每个数字的出现次数（0-9）
        let mut count = [0; 10];
        for &d in &digits {
            count[d as usize] += 1;
        }

        let mut result = Vec::new();

        // 枚举百位数（不能为0）
        for hundreds in 1..=9 {
            if count[hundreds as usize] == 0 {
                continue;
            }
            count[hundreds as usize] -= 1; // 使用一个百位数

            // 枚举十位数（可以为0）
            for tens in 0..=9 {
                if count[tens as usize] == 0 {
                    continue;
                }
                count[tens as usize] -= 1; // 使用一个十位数

                // 枚举个位数（必须为偶数）
                for units in (0..=8).step_by(2) {
                    if count[units as usize] == 0 {
                        continue;
                    }

                    // 组成三位数并加入结果
                    let num = hundreds * 100 + tens * 10 + units;
                    result.push(num);
                }

                count[tens as usize] += 1; // 恢复十位数
            }

            count[hundreds as usize] += 1; // 恢复百位数
        }

        result.sort();
        result
    }
}
```
