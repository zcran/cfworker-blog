---
title: "leetcode-计数3"
date: 2026-08-01T10:40:50+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 前 K 个高频元素

给你一个整数数组 nums 和一个整数 k ，请你返回其中出现频率前 k 高的元素。你可以按 任意顺序 返回答案。


```
use std::collections::HashMap;

impl Solution {
    /// 返回数组中出现频率前 k 高的元素
    ///
    /// # 算法思路
    /// 使用**桶排序**（计数排序变体）：
    /// 1. 统计每个元素的出现次数（哈希表）
    /// 2. 将元素按出现次数放入对应的桶中
    /// 3. 从高到低遍历桶，收集出现次数最多的 k 个元素
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为数组长度
    /// - 空间：O(n)，用于存储哈希表和桶
    pub fn top_k_frequent(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let n = nums.len();

        // 1. 统计频率：数字 -> 出现次数
        let mut freq_map = HashMap::with_capacity(n);
        for num in nums {
            *freq_map.entry(num).or_insert(0) += 1;
        }

        // 2. 桶排序：索引 = 出现次数，值 = 该频率对应的数字列表
        // 最大频率不会超过数组长度 n
        let mut buckets = vec![Vec::new(); n + 1];
        for (num, count) in freq_map {
            buckets[count as usize].push(num);
        }

        // 3. 从高频到低频收集结果
        let mut result = Vec::with_capacity(k);
        for bucket in buckets.into_iter().rev() {
            for num in bucket {
                result.push(num);
                if result.len() == k {
                    return result;
                }
            }
        }

        // 题目保证 k 有效，这里不会执行
        unreachable!()
    }
}
```
