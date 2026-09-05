---
title: "leetcode-前缀和5"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 按权重随机选择

给你一个 下标从 0 开始 的正整数数组 w ，其中 w[i] 代表第 i 个下标的权重。

请你实现一个函数 pickIndex ，它可以 随机地 从范围 [0, w.length - 1] 内（含 0 和 w.length - 1）选出并返回一个下标。选取下标 i 的 概率 为 w[i] / sum(w) 。

例如，对于 w = [1, 3]，挑选下标 0 的概率为 1 / (1 + 3) = 0.25 （即，25%），而选取下标 1 的概率为 3 / (1 + 3) = 0.75（即，75%）。


```
use rand::Rng;

/// 按权重随机选择下标
///
/// 核心思路：前缀和 + 二分查找
///
/// 1. 构建前缀和数组 prefix，其中 prefix[i] = w[0] + w[1] + ... + w[i]
/// 2. 随机生成一个数 target ∈ [0, total_weight)
/// 3. 使用二分查找找到第一个 prefix[i] > target 的位置 i
/// 4. 位置 i 即为按权重随机选择的下标
///
/// 时间复杂度：构造 O(n)，查询 O(log n)
/// 空间复杂度：O(n)
struct Solution {
    prefix: Vec<i32>,  // 前缀和数组
    total: i32,        // 总权重
}

impl Solution {
    /// 构造函数：初始化前缀和数组
    pub fn new(w: Vec<i32>) -> Self {
        let mut prefix = Vec::with_capacity(w.len());
        let mut sum = 0;
        for &weight in &w {
            sum += weight;
            prefix.push(sum);
        }
        Self { prefix, total: sum }
    }

    /// 按权重随机选择一个下标
    ///
    /// 算法：
    /// 1. 在 [0, total) 范围内生成随机数 target
    /// 2. 二分查找第一个 > target 的前缀和位置
    /// 3. 返回该位置索引
    pub fn pick_index(&self) -> i32 {
        // 生成随机数 [0, total)
        let target = rand::thread_rng().gen_range(0..self.total);

        // 二分查找第一个 > target 的位置
        let mut left = 0;
        let mut right = self.prefix.len();

        while left < right {
            let mid = left + (right - left) / 2;
            if self.prefix[mid] > target {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        left as i32
    }
}
```
