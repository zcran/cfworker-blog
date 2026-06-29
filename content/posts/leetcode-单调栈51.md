---
title: "leetcode-单调栈51"
date: 2026-06-07T10:14:06+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 统计好子数组

给你一个整数数组 nums。

如果一个 子数组 中所有元素的 按位或 等于该子数组中 至少出现一次 的元素，则称其为 好 子数组。

返回 nums 中好子数组的数量。

子数组 是数组中一段连续的 非空 元素序列。

这里，两个整数 a 和 b 的按位或表示为 a | b。


```
impl Solution {
    /// 计算好子数组的数量
    /// 好子数组：子数组所有元素的按位或等于该子数组中至少出现一次的元素
    ///
    /// 核心思路：
    /// 1. 维护所有以当前 i 为右端点的子数组的 OR 值（最多 32 种，因为整数范围）
    /// 2. 对于每个 OR 值，找到满足条件的左端点范围
    /// 3. 利用哈希表记录每个值最后出现的位置，快速判断 OR 值是否在子数组中出现过
    pub fn count_good_subarrays(nums: Vec<i32>) -> i64 {
        let mut ans = 0i64;
        // 存储 (OR值, 最小左端点)，保持 OR 值互异且单调递增
        let mut or_left: Vec<(i32, usize)> = Vec::with_capacity(32);
        // 记录每个值最后出现的位置
        let mut last_occurrence = std::collections::HashMap::with_capacity(nums.len());

        for (i, &x) in nums.iter().enumerate() {
            // 更新当前值的最新位置
            last_occurrence.insert(x, i);

            // 更新所有已有的 OR 值（合并当前元素）
            for (or_val, _) in or_left.iter_mut() {
                *or_val |= x;
            }
            // 当前元素单独作为子数组
            or_left.push((x, i));

            // 去重：相同 OR 值只保留最左边的那个（因为左端点越小，覆盖范围越大）
            let mut write_idx = 1;
            for read_idx in 1..or_left.len() {
                if or_left[read_idx].0 != or_left[read_idx - 1].0 {
                    or_left[write_idx] = or_left[read_idx];
                    write_idx += 1;
                }
            }
            or_left.truncate(write_idx);

            // 对于每个不同的 OR 值，计算贡献
            for k in 0..or_left.len() {
                let (or_val, left) = or_left[k];
                // 当前 OR 值对应的左端点范围：[left, right]
                let right = if k + 1 < or_left.len() {
                    or_left[k + 1].1 - 1
                } else {
                    i
                };

                // 检查 OR 值是否在子数组中出现过
                if let Some(&pos) = last_occurrence.get(&or_val) {
                    if pos >= left {
                        // 左端点可以在 [left, min(right, pos)] 范围内
                        let end = right.min(pos);
                        ans += (end - left + 1) as i64;
                    }
                }
            }
        }

        ans
    }
}
```
