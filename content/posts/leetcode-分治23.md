---
title: "leetcode-分治23"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 裁剪数字后查询第 K 小的数字


给你一个下标从 0 开始的字符串数组 nums ，其中每个字符串 长度相等 且只包含数字。

再给你一个下标从 0 开始的二维整数数组 queries ，其中 queries[i] = [ki, trimi] 。对于每个 queries[i] ，你需要：

· 将 nums 中每个数字 裁剪 到剩下 最右边 trimi 个数位。
· 在裁剪过后的数字中，找到 nums 中第 ki 小数字对应的 下标 。如果两个裁剪后数字一样大，那么下标 更小 的数字视为更小的数字。
· 将 nums 中每个数字恢复到原本字符串。

请你返回一个长度与 queries 相等的数组 answer，其中 answer[i]是第 i 次查询的结果。

提示：

· 裁剪到剩下最右边 x 个数位的意思是不断删除最左边的数位，直到剩下 x 个数位。
· nums 中的字符串可能会有前导 0 。



```
impl Solution {
    /// 返回每个查询的结果：裁剪后第 k 小的数字在原数组中的下标
    ///
    /// # 参数
    /// - `nums`: 长度相等的数字字符串数组
    /// - `queries`: 查询数组，每个查询为 [ki, trimi]
    ///
    /// # 返回
    /// - 每个查询对应的原下标（0-indexed）
    ///
    /// # 算法思路
    /// 1. 将所有查询按裁剪长度 trimi 分组排序
    /// 2. 使用基数排序（LSD，从最低位开始），每次增加一位时复用之前的排序结果
    /// 3. 对于每个查询，在已排序的索引数组中找到第 ki 小的元素下标
    pub fn smallest_trimmed_numbers(nums: Vec<String>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let num_count = nums.len();           // 数字个数 m
        let str_len = nums[0].len();          // 字符串长度 n

        // 为每个查询附加原始索引，便于最后按序输出
        let mut indexed_queries: Vec<(usize, usize, usize)> = queries
            .into_iter()
            .enumerate()
            .map(|(orig_idx, q)| (orig_idx, q[0] as usize, q[1] as usize))
            .collect();

        // 按裁剪长度升序排序（trimi 小的先处理）
        indexed_queries.sort_by_key(|&(_, _, trim_len)| trim_len);

        // 当前已排序到的位数（从右往左数，已处理了多少位）
        let mut processed_rounds = 0;

        // 当前索引顺序（按已处理的位数排序后的原数组下标）
        let mut indices: Vec<usize> = (0..num_count).collect();

        // 辅助数组，用于基数排序时暂存结果
        let mut temp_indices = vec![0; num_count];

        // 答案数组，长度为查询数量
        let mut answers = vec![0; indexed_queries.len()];

        // 按裁剪长度依次处理查询
        for (orig_idx, kth, trim_len) in indexed_queries {
            // 如果当前查询需要更多的位数，则继续基数排序
            while processed_rounds < trim_len {
                // 当前要排序的位（从右往左数，0 是最低位）
                let digit_pos = str_len - 1 - processed_rounds;

                // 计数排序：统计当前位每个数字（0-9）的出现次数
                let mut counts = [0; 10];
                for &idx in &indices {
                    let digit = (nums[idx].as_bytes()[digit_pos] - b'0') as usize;
                    counts[digit] += 1;
                }

                // 计算前缀和，得到每个数字的最终位置范围
                for i in 1..10 {
                    counts[i] += counts[i - 1];
                }

                // 稳定排序：从后往前遍历，保持相同数字下原有的相对顺序
                for &idx in indices.iter().rev() {
                    let digit = (nums[idx].as_bytes()[digit_pos] - b'0') as usize;
                    counts[digit] -= 1;
                    temp_indices[counts[digit]] = idx;
                }

                // 交换排序结果，准备下一轮
                std::mem::swap(&mut indices, &mut temp_indices);
                processed_rounds += 1;
            }

            // 取出第 kth 小的元素对应的原下标（kth 是 1-indexed）
            answers[orig_idx] = indices[kth - 1] as i32;
        }

        answers
    }
}

```
