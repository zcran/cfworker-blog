---
title: "leetcode-矩阵85"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 珠宝的最高价值

现有一个记作二维矩阵 frame 的珠宝架，其中 frame[i][j] 为该位置珠宝的价值。拿取珠宝的规则为：

只能从架子的左上角开始拿珠宝
每次可以移动到右侧或下侧的相邻位置
到达珠宝架子的右下角时，停止拿取

注意：珠宝的价值都是大于 0 的。除非这个架子上没有任何珠宝，比如 frame = [[0]]。


```
impl Solution {
    pub fn jewellery_value(frame: Vec<Vec<i32>>) -> i32 {
        let rows = frame.len();
        let cols = frame[0].len();

        // dp[j]：到达当前行第 j 列时的最大价值（滚动数组，多开 1 位方便处理）
        let mut dp = vec![0; cols + 1];

        for i in 0..rows {
            for j in 0..cols {
                // 从上方 dp[j+1] 或左方 dp[j] 转移，加上当前格子价值
                dp[j + 1] = dp[j + 1].max(dp[j]) + frame[i][j];
            }
        }

        dp[cols]
    }
}
```
