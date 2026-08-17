---
title: "leetcode-模拟102"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 边界上的蚂蚁

边界上有一只蚂蚁，它有时向 左 走，有时向 右 走。

给你一个 非零 整数数组 nums 。蚂蚁会按顺序读取 nums 中的元素，从第一个元素开始直到结束。每一步，蚂蚁会根据当前元素的值移动：

· 如果 nums[i] < 0 ，向 左 移动 -nums[i]单位。
· 如果 nums[i] > 0 ，向 右 移动 nums[i]单位。

返回蚂蚁 返回 到边界上的次数。

注意：

· 边界两侧有无限的空间。
· 只有在蚂蚁移动了 |nums[i]| 单位后才检查它是否位于边界上。换句话说，如果蚂蚁只是在移动过程中穿过了边界，则不会计算在内。


```
impl Solution {
 pub fn return_to_boundary_count(nums: Vec<i32>) -> i32 {
     let mut pos = 0;  // 蚂蚁当前位置（从边界出发）
     let mut cnt = 0;  // 返回边界次数

     for &x in &nums {
         pos += x;       // 移动
         if pos == 0 {   // 移动后恰好在边界上
             cnt += 1;
         }
     }

     cnt
 }
}
```
