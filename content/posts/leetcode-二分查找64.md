---
title: "leetcode-二分查找64"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 修车的最少时间

给你一个整数数组 ranks ，表示一些机械工的 能力值 。ranksi 是第 i 位机械工的能力值。能力值为 r 的机械工可以在 r * n2 分钟内修好 n 辆车。

同时给你一个整数 cars ，表示总共需要修理的汽车数目。

请你返回修理所有汽车 最少 需要多少时间。

注意：所有机械工可以同时修理汽车。


```
impl Solution {
    pub fn repair_cars(ranks: Vec<i32>, cars: i32) -> i64 {
        let cars = cars as i64;
        // 能力值最小的工人单独修完所有车，是最坏情况的上界
        let mut left = 1_i64;
        let mut right = ranks.iter().map(|&r| r as i64).min().unwrap() * cars * cars;

        // 判断在时间 t 内，所有工人能否修完至少 cars 辆车
        let can_finish = |t: i64| -> bool {
            let mut total = 0_i64;
            for &r in &ranks {
                total += (t / r as i64).isqrt();
                if total >= cars {
                    return true;
                }
            }
            false
        };

        while left < right {
            let mid = left + (right - left) / 2;
            if can_finish(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left
    }
}
```
