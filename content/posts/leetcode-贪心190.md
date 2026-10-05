---
title: "leetcode-贪心190"
date: 2026-10-03T12:55:47+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 峰与谷

在一个整数数组中，“峰”是大于或等于相邻整数的元素，相应地，“谷”是小于或等于相邻整数的元素。例如，在数组{5, 8, 4, 2, 3, 4, 6}中，{8, 6}是峰， {5, 2}是谷。现在给定一个整数数组，将该数组按峰与谷的交替顺序排序。


```
impl Solution {
    pub fn wiggle_sort(nums: &mut Vec<i32>) {
        // 目标模式：谷 < 峰 > 谷 < 峰 ...
        // 即偶数下标应为谷（不大于相邻），奇数下标应为峰（不小于相邻）。
        // 只需一次线性扫描，逐对修正相邻元素即可。
        for i in 0..nums.len().saturating_sub(1) {
            let want_peak = i % 2 == 1;
            let out_of_order = if want_peak {
                nums[i] < nums[i + 1] // 峰应 >= 右邻
            } else {
                nums[i] > nums[i + 1] // 谷应 <= 右邻
            };
            if out_of_order {
                nums.swap(i, i + 1);
            }
        }
    }
}
```
