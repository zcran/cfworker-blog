---
title: "leetcode-分治36"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 划分二进制字符串的最小费用


给你一个二进制字符串 s 和两个整数 encCost 与 flatCost。

对于每个下标 i，s[i] = '1' 表示第 i 个元素是敏感的，而 s[i] = '0' 表示它不是敏感的。

该字符串必须被划分为 分段。最初，整个字符串形成一个单一的分段。

对于一个长度为 L 且包含 X 个敏感元素的分段:

· 如果 X = 0，费用为 flatCost。
· 如果 X > 0，费用为 L * X * encCost。

如果一个分段具有 偶数长度，你可以将其拆分为两个长度 相等 的 连续分段，此次拆分的费用是所得分段的 费用之和。

返回一个整数，表示所有有效划分中的 最小可能总费用。

```
impl Solution {
    /// 计算将字符串转换为全零字符串的最小成本
    ///
    /// # 参数
    /// - `s`: 由 '0' 和 '1' 组成的字符串
    /// - `enc_cost`: 编码成本，将一段连续的 '1' 编码的成本系数
    /// - `flat_cost`: 平坦成本，当段中没有 '1' 时的固定成本
    ///
    /// # 返回
    /// 最小转换成本
    ///
    /// # 算法说明
    /// 1. 首先计算整体编码成本（所有 '1' 一起处理）
    /// 2. 如果字符串长度为偶数，尝试将字符串分成两半分别处理
    /// 3. 递归计算分段处理的最小成本
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// assert_eq!(Solution::min_cost("1010".to_string(), 1, 2), 2);
    /// ```
    pub fn min_cost(s: String, enc_cost: i32, flat_cost: i32) -> i64 {
        // 将字符串转换为字节数组，方便处理
        let bytes = s.into_bytes();
        let length = bytes.len();
        let target_char = b'1'; // 目标字符为 '1'

        // 构建前缀和数组：prefix_ones[i] 表示前 i 个字符中 '1' 的数量
        // 这样可以在 O(1) 时间内查询任意区间的 '1' 的个数
        let mut prefix_ones = Vec::with_capacity(length);
        let mut total_ones = 0;
        for &ch in &bytes {
            if ch == target_char {
                total_ones += 1;
            }
            prefix_ones.push(total_ones);
        }

        // 计算整体处理的成本（不拆分的情况）
        // 如果全为 '0'，成本为 flat_cost；否则成本为 段数 × '1'的个数 × enc_cost
        let no_split_cost = if total_ones == 0 {
            flat_cost as i64
        } else {
            length as i64 * total_ones as i64 * enc_cost as i64
        };

        // 定义递归函数：计算区间 [left, right] 的最小成本
        // 使用闭包捕获外部变量，但由于递归需要，定义为独立的函数
        fn calculate_min_cost(
            prefix_ones: &[i32],
            left: usize,
            right: usize,
            enc_cost: &i64,
            flat_cost: &i64,
        ) -> i64 {
            // 计算当前区间中 '1' 的数量
            let ones_before = if left == 0 { 0 } else { prefix_ones[left - 1] };
            let ones_count = prefix_ones[right] - ones_before;
            let total_count = right - left + 1;

            // 当前区间不拆分的成本
            let mut current_cost = if ones_count == 0 {
                *flat_cost // 没有 '1'，使用平坦成本
            } else {
                total_count as i64 * ones_count as i64 * enc_cost
            };

            // 如果区间长度为偶数，尝试将区间对半分，分别计算成本并取最小值
            if total_count % 2 == 0 {
                let half_count = total_count / 2;
                let left_cost = calculate_min_cost(
                    prefix_ones,
                    left,
                    left + half_count - 1,
                    enc_cost,
                    flat_cost,
                );
                let right_cost = calculate_min_cost(
                    prefix_ones,
                    right - half_count + 1,
                    right,
                    enc_cost,
                    flat_cost,
                );
                current_cost = current_cost.min(left_cost + right_cost);
            }

            current_cost
        }

        // 如果字符串长度为偶数，尝试将整个字符串分成两半处理
        if length % 2 == 0 {
            let half_count = length / 2;
            let enc_cost = enc_cost as i64;
            let flat_cost = flat_cost as i64;

            let left_cost = calculate_min_cost(&prefix_ones, 0, half_count - 1, &enc_cost, &flat_cost);
            let right_cost = calculate_min_cost(&prefix_ones, half_count, length - 1, &enc_cost, &flat_cost);
            let split_cost = left_cost + right_cost;

            // 返回不拆分和拆分两种方案中的最小值
            no_split_cost.min(split_cost)
        } else {
            // 字符串长度为奇数，不能拆分，直接返回整体成本
            no_split_cost
        }
    }
}
```
