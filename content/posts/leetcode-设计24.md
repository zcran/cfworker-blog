---
title: "leetcode-设计24"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 找出和为指定值的下标对

给你两个整数数组 nums1 和 nums2 ，请你实现一个支持下述两类查询的数据结构：

1. 累加 ，将一个正整数加到 nums2 中指定下标对应元素上。
2. 计数 ，统计满足 nums1[i] + nums2[j] 等于指定值的下标对 (i, j) 数目（0 <= i < nums1.length 且 0 <= j < nums2.length）。

实现 FindSumPairs 类：

· FindSumPairs(int[] nums1, int[] nums2) 使用整数数组 nums1 和 nums2 初始化 FindSumPairs 对象。
· void add(int index, int val) 将 val 加到 nums2[index] 上，即，执行 nums2[index] += val 。
· int count(int tot) 返回满足 nums1[i] + nums2[j] == tot 的下标对 (i, j) 数目。


```
use std::collections::HashMap;

/// 查找和配对数据结构
///
/// 维护 nums2 的频率表，count 时遍历 nums1 查询
/// 时间复杂度: add O(1), count O(n1)
/// 空间复杂度: O(n2)
struct FindSumPairs {
    nums1: Vec<i32>,
    nums2: Vec<i32>,
    freq: HashMap<i32, i32>, // nums2 中每个值的出现次数
}

impl FindSumPairs {
    /// 初始化，统计 nums2 的频率
    fn new(nums1: Vec<i32>, nums2: Vec<i32>) -> Self {
        let mut freq = HashMap::with_capacity(nums2.len());
        for &num in &nums2 {
            *freq.entry(num).or_insert(0) += 1;
        }

        FindSumPairs { nums1, nums2, freq }
    }

    /// 将 val 加到 nums2[index]，并更新频率表
    fn add(&mut self, index: i32, val: i32) {
        let idx = index as usize;
        let old = self.nums2[idx];
        let new = old + val;

        // 更新频率表: 移除旧值，添加新值
        if let Some(count) = self.freq.get_mut(&old) {
            *count -= 1;
            if *count == 0 {
                self.freq.remove(&old);
            }
        }

        self.nums2[idx] = new;
        *self.freq.entry(new).or_insert(0) += 1;
    }

    /// 统计满足 nums1[i] + nums2[j] == tot 的对数
    fn count(&self, tot: i32) -> i32 {
        self.nums1
            .iter()
            .filter_map(|&x| self.freq.get(&(tot - x)))
            .sum()
    }
}
```
