---
title: "leetcode-二分查找73"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 范围内整数的最大得分

给你一个整数数组 start 和一个整数 d，代表 n 个区间 [start[i], start[i] + d]。

你需要选择 n 个整数，其中第 i 个整数必须属于第 i 个区间。所选整数的 得分 定义为所选整数两两之间的 最小 绝对差。

返回所选整数的 最大可能得分 。


```
impl Solution {
    pub fn max_possible_score(mut start: Vec<i32>, d: i32) -> i32 {
        start.sort_unstable();

        // 判断是否存在一种选法，使所有相邻所选整数的差至少为 score
        let can_achieve = |score: i32| -> bool {
            let mut prev = i64::MIN;
            for &s in &start {
                // 在当前区间内选一个不小于 prev + score 的最小整数
                prev = (prev + score as i64).max(s as i64);
                if prev > s as i64 + d as i64 {
                    return false;
                }
            }
            true
        };

        // 二分最大得分，上界为区间跨度 + d
        let mut left = 0;
        let mut right = start[start.len() - 1] - start[0] + d;
        while left <= right {
            let mid = left + (right - left) / 2;
            if can_achieve(mid) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        right
    }
}
```
