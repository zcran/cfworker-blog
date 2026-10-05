---
title: "leetcode-双指针140"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最小差

给定两个整数数组a和b，计算具有最小差绝对值的一对数值（每个数组中取一个值），并返回该对数值的差


```
impl Solution {
    pub fn smallest_difference(mut a: Vec<i32>, mut b: Vec<i32>) -> i32 {
        a.sort_unstable();
        b.sort_unstable();

        let (mut i, mut j) = (0usize, 0usize);
        let mut min_diff = i64::MAX;

        // 双指针：每次比较 a[i] 与 b[j]，较小的一侧前移，
        // 因为增大较小值才有可能缩小差值。
        while i < a.len() && j < b.len() {
            let diff = (a[i] as i64 - b[j] as i64).abs();
            if diff < min_diff {
                min_diff = diff;
                if min_diff == 0 {
                    return 0; // 已是最小可能差值，提前退出
                }
            }

            if a[i] < b[j] {
                i += 1;
            } else {
                j += 1;
            }
        }

        min_diff as i32
    }
}
```
