---
title: "leetcode-矩阵41"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最小化目标值与所选元素的差

给你一个大小为 m x n 的整数矩阵 mat 和一个整数 target 。

从矩阵的 每一行 中选择一个整数，你的目标是 最小化 所有选中元素之 和 与目标值 target 的 绝对差 。

返回 最小的绝对差 。

a 和 b 两数字的 绝对差 是 a - b 的绝对值。


```
impl Solution {
    /// 逐行 DP，布尔数组记录 0..=target 的可行和。
    /// 超过 target 的部分只保留最小的超出值（因为元素非负，超出后只会更大）。
    /// 时间复杂度：O(m * n * target)，空间复杂度：O(target)。
    pub fn minimize_the_difference(mat: Vec<Vec<i32>>, target: i32) -> i32 {
        let cap = target as usize;
        let mut possible = vec![false; cap + 1];
        possible[0] = true;
        // 当前可达的、严格大于 target 的最小和
        let mut exceed = i32::MAX;

        for row in &mat {
            let mut next = vec![false; cap + 1];
            // 若上一行已有超出值，加本行最小元素得到本行一个候选超出值
            let min_v = *row.iter().min().unwrap();
            let mut nexc = if exceed == i32::MAX {
                i32::MAX
            } else {
                exceed + min_v
            };

            for (sum, &ok) in possible.iter().enumerate() {
                if !ok {
                    continue;
                }
                for &v in row {
                    let s = sum as i32 + v;
                    if s <= target {
                        next[s as usize] = true;
                    } else if s < nexc {
                        nexc = s;
                    }
                }
            }

            possible = next;
            exceed = nexc;
        }

        let mut best = i32::MAX;
        for (sum, &ok) in possible.iter().enumerate() {
            if ok {
                best = best.min((sum as i32 - target).abs());
            }
        }
        if exceed != i32::MAX {
            best = best.min((exceed - target).abs());
        }
        best
    }
}
```
