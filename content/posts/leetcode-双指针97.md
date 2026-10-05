---
title: "leetcode-双指针97"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 统计公平数对的数目

给你一个下标从 0 开始、长度为 n 的整数数组 nums ，和两个整数 lower 和 upper ，返回 公平数对的数目 。

如果 (i, j) 数对满足以下情况，则认为它是一个 公平数对 ：

0 <= i < j < n，且

lower <= nums[i] + nums[j] <= upper


```
impl Solution {
    pub fn count_fair_pairs(mut nums: Vec<i32>, lower: i32, upper: i32) -> i64 {
        nums.sort_unstable();
        let mut count = 0i64;

        // 对每个 nums[i]，在它左侧的前缀中二分统计满足范围的元素个数
        for i in 1..nums.len() {
            let (lo, hi) = (&nums[..i], &nums[..i]);
            let left = lo.partition_point(|&x| (x as i64) + (nums[i] as i64) < lower as i64);
            let right = hi.partition_point(|&x| (x as i64) + (nums[i] as i64) <= upper as i64);
            count += (right - left) as i64;
        }

        count
    }
}
```
