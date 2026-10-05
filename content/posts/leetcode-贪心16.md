---
title: "leetcode-贪心16"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 种花问题

假设有一个很长的花坛，一部分地块种植了花，另一部分却没有。可是，花不能种植在相邻的地块上，它们会争夺水源，两者都会死去。

给你一个整数数组 flowerbed 表示花坛，由若干 0 和 1 组成，其中 0 表示没种植花，1 表示种植了花。另有一个数 n ，能否在不打破种植规则的情况下种入 n 朵花？能则返回 true ，不能则返回 false 。


```
impl Solution {
    pub fn can_place_flowers(flowerbed: Vec<i32>, n: i32) -> bool {
        let (m, mut n, mut i) = (flowerbed.len(), n, 0);

        while i < m {
            // 当前位置为空，且左右相邻位置（若存在）也为空，则可种花
            let left_ok  = i == 0     || flowerbed[i - 1] == 0;
            let right_ok = i == m - 1 || flowerbed[i + 1] == 0;

            if left_ok && flowerbed[i] == 0 && right_ok {
                n -= 1;
                i += 2; // 下一格与当前格相邻，必定不能再种，直接跳过
            } else {
                i += 1;
            }

            // 提前返回：已种够 n 朵
            if n <= 0 {
                return true;
            }
        }

        n <= 0
    }
}
```
