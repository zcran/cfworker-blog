---
title: "leetcode-贪心29"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 三角形的最大周长

给定由一些正数（代表长度）组成的数组 nums ，返回 由其中三个长度组成的、面积不为零的三角形的最大周长 。如果不能形成任何面积不为零的三角形，返回 0。


```
impl Solution {
    pub fn largest_perimeter(mut nums: Vec<i32>) -> i32 {
        // 排序后从后往前找：最大的三条边若能构成三角形（两边之和大于第三边），
        // 其周长即为最大；否则继续向前检查更小的三条边
        nums.sort_unstable();

        for w in nums.windows(3).rev() {
            if w[0] + w[1] > w[2] {
                return w[0] + w[1] + w[2];
            }
        }

        0
    }
}
```
