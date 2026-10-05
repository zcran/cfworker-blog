---
title: "leetcode-二分查找10"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最长递增子序列

给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。


```
impl Solution {
    pub fn length_of_lis(nums: Vec<i32>) -> i32 {
        // tails[i] 表示长度为 i+1 的递增子序列中，末尾元素的最小值。
        // tails 本身严格递增，可二分。
        let mut tails: Vec<i32> = Vec::with_capacity(nums.len());

        for &x in &nums {
            // 找到第一个 >= x 的位置（lower_bound）
            match tails.binary_search(&x) {
                // x 已在 tails 中，无需改动（严格递增子序列中重复值不延长长度）
                Ok(_) => {}
                // 用 x 替换第一个 > x 的元素，使该长度的末尾更小
                Err(pos) => {
                    if pos == tails.len() {
                        tails.push(x);
                    } else {
                        tails[pos] = x;
                    }
                }
            }
        }

        tails.len() as i32
    }
}
```
