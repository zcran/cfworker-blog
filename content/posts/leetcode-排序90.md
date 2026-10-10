---
title: "leetcode-排序90"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 堆箱子

堆箱子。给你一堆n个箱子，箱子宽 wi、深 di、高 hi。箱子不能翻转，将箱子堆起来时，下面箱子的宽度、高度和深度必须大于上面的箱子。实现一种方法，搭出最高的一堆箱子。箱堆的高度为每个箱子高度的总和。

输入使用数组[wi, di, hi]表示每个箱子。




```
impl Solution {
    pub fn pile_box(mut boxes: Vec<Vec<i32>>) -> i32 {
        // 按宽度升序排序，保证 dp 时只可能从更小的箱子转移过来
        boxes.sort_unstable_by_key(|b| b[0]);
        let n = boxes.len();

        // dp[i]：以第 i 个箱子为顶部时能搭出的最大高度
        let mut dp = vec![0; n];
        let mut ans = 0;

        for i in 0..n {
            let (wi, di, hi) = (boxes[i][0], boxes[i][1], boxes[i][2]);
            let mut best = 0;
            for j in 0..i {
                // 下面箱子的宽、深、高都必须严格大于上面箱子
                if wi > boxes[j][0] && di > boxes[j][1] && hi > boxes[j][2] {
                    best = best.max(dp[j]);
                }
            }
            dp[i] = best + hi;
            ans = ans.max(dp[i]);
        }

        ans
    }
}
```
