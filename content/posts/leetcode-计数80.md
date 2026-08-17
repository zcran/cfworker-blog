---
title: "leetcode-计数80"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


##  正整数和负整数的最大计数


给你一个按 非递减顺序 排列的数组 nums ，返回正整数数目和负整数数目中的最大值。

换句话讲，如果 nums 中正整数的数目是 pos ，而负整数的数目是 neg ，返回 pos 和 neg二者中的最大值。

注意：0 既不是正整数也不是负整数。

```
impl Solution {
    pub fn maximum_count(nums: Vec<i32>) -> i32 {
        // 数组有序，可用二分查找快速定位
        // 第一个 >= 0 的位置 -> 负数个数
        // 第一个 > 0 的位置 -> 非正数个数，正数个数 = len - 该位置
        let neg = nums.partition_point(|&x| x < 0);
        let pos = nums.len() - nums.partition_point(|&x| x <= 0);
        neg.max(pos) as i32
    }
}
```
