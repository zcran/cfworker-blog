---
title: "leetcode-贪心91"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 从数组中移除最大值和最小值

给你一个下标从 0 开始的数组 nums ，数组由若干 互不相同 的整数组成。

nums 中有一个值最小的元素和一个值最大的元素。分别称为 最小值 和 最大值 。你的目标是从数组中移除这两个元素。

一次 删除 操作定义为从数组的 前面 移除一个元素或从数组的 后面 移除一个元素。

返回将数组中最小值和最大值 都 移除需要的最小删除次数。


```
impl Solution {
    pub fn minimum_deletions(nums: Vec<i32>) -> i32 {
        // 一次遍历同时找到最小值和最大值的下标
        let (mut min_i, mut max_i) = (0, 0);
        for (i, &x) in nums.iter().enumerate() {
            if x < nums[min_i] { min_i = i; }
            if x > nums[max_i] { max_i = i; }
        }

        // l, r 分别为两个目标下标中的较小者和较大者
        let (l, r) = (min_i.min(max_i), min_i.max(max_i));
        let n = nums.len();

        // 三种删除策略取最小：
        // 1. 全部从右侧删除：需删到 l 位置，共 n - l 次
        // 2. 全部从左侧删除：需删到 r 位置，共 r + 1 次
        // 3. 左侧删到 l，右侧删到 r：共 (l + 1) + (n - r) 次
        (n - l).min(r + 1).min(l + 1 + n - r) as i32
    }
}
```
