---
title: "leetcode-设计8"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 打乱数组

给你一个整数数组 nums ，设计算法来打乱一个没有重复元素的数组。打乱后，数组的所有排列应该是 等可能 的。

实现 Solution class:

Solution(int[] nums) 使用整数数组 nums 初始化对象
int[] reset() 重设数组到它的初始状态并返回
int[] shuffle() 返回数组随机打乱后的结果


```
use rand::seq::SliceRandom;
use rand::thread_rng;

/// Fisher-Yates 洗牌算法实现，保证所有排列等概率出现
struct Solution {
    /// 原始数组，用于 reset 操作
    original: Vec<i32>,
}

impl Solution {
    /// 使用整数数组初始化
    ///
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn new(nums: Vec<i32>) -> Self {
        Self {
            original: nums,
        }
    }

    /// 重置数组到初始状态
    ///
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn reset(&self) -> Vec<i32> {
        self.original.clone()
    }

    /// 返回数组的一个随机排列，所有排列等概率
    ///
    /// 使用 Fisher-Yates 洗牌算法（也称为 Knuth 洗牌）
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn shuffle(&self) -> Vec<i32> {
        let mut shuffled = self.original.clone();
        shuffled.shuffle(&mut thread_rng());
        shuffled
    }
}
```
