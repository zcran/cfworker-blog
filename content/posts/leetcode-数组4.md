---
title: "leetcode-数组4"
date: 2026-10-10T20:38:40+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最大连续 1 的个数

给定一个二进制数组 nums ， 计算其中最大连续 1 的个数。


```
impl Solution {
    /// 计算二进制数组中最大连续 1 的个数。
    ///
    /// 一次遍历，遇到 1 则累加当前连续长度并更新最大值；
    /// 遇到 0 则将当前连续长度清零。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn find_max_consecutive_ones(nums: Vec<i32>) -> i32 {
        let mut max = 0;
        let mut cur = 0;
        for x in nums {
            cur = if x == 1 { cur + 1 } else { 0 };
            max = max.max(cur);
        }
        max
    }
}
```
