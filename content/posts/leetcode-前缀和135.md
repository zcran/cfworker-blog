---
title: "leetcode-前缀和135"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最小稳定下标 I

给你一个长度为 n 的整数数组 nums 和一个整数 k。

对于每个下标 i，定义它的 不稳定值 为 max(nums[0..i]) - min(nums[i..n - 1])。

换句话说：

max(nums[0..i]) 表示从下标 0 到下标 i 的元素中的 最大值 。

min(nums[i..n - 1]) 表示从下标 i 到下标 n - 1 的元素中的 最小值 。

如果某个下标 i 的不稳定值 小于等于 k，则称该下标为 稳定下标 。

返回 最小 的稳定下标。如果不存在这样的下标，则返回 -1。


```
impl Solution {
    pub fn first_stable_index(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        if n == 0 {
            return -1;
        }

        // 前缀最大值: pre_max[i] = max(nums[0..=i])
        let mut pre_max = vec![0; n];
        pre_max[0] = nums[0];
        for i in 1..n {
            pre_max[i] = pre_max[i - 1].max(nums[i]);
        }

        // 后缀最小值: suf_min[i] = min(nums[i..n-1])
        let mut suf_min = vec![0; n];
        suf_min[n - 1] = nums[n - 1];
        for i in (0..n - 1).rev() {
            suf_min[i] = suf_min[i + 1].min(nums[i]);
        }

        // 从左到右找到第一个满足条件的下标
        for i in 0..n {
            if pre_max[i] - suf_min[i] <= k {
                return i as i32;
            }
        }

        -1
    }
}
```
