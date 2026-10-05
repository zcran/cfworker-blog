---
title: "leetcode-二分查找58"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 分组的最大数量

给你一个正整数数组 grades ，表示大学中一些学生的成绩。你打算将 所有 学生分为一些 有序 的非空分组，其中分组间的顺序满足以下全部条件：

第 i 个分组中的学生总成绩 小于 第 (i + 1) 个分组中的学生总成绩，对所有组均成立（除了最后一组）。

第 i 个分组中的学生总数 小于 第 (i + 1) 个分组中的学生总数，对所有组均成立（除了最后一组）。

返回可以形成的 最大 组数。


```
impl Solution {
    pub fn maximum_groups(grades: Vec<i32>) -> i32 {
        // 组数只与总人数有关：k 个分组至少需要 1+2+...+k = k(k+1)/2 人
        // 求最大的 k 使得 k(k+1)/2 <= n
        let n = grades.len() as i64;
        let mut lo = 0i64;
        let mut hi = n; // 组数不会超过总人数

        while lo < hi {
            let mid = lo + (hi - lo + 1) / 2;
            if mid * (mid + 1) / 2 <= n {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }

        lo as i32
    }
}
```
