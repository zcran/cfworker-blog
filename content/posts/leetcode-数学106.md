---
title: "leetcode-数学106"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 黑白方格画

小扣注意到秋日市集上有一个创作黑白方格画的摊位。摊主给每个顾客提供一个固定在墙上的白色画板，画板不能转动。画板上有 n * n 的网格。绘画规则为，小扣可以选择任意多行以及任意多列的格子涂成黑色（选择的整行、整列均需涂成黑色），所选行数、列数均可为 0。

小扣希望最终的成品上需要有 k 个黑色格子，请返回小扣共有多少种涂色方案。

注意：两个方案中任意一个相同位置的格子颜色不同，就视为不同的方案。


```
impl Solution {
    pub fn painting_plan(n: i32, k: i32) -> i32 {
        // 全部涂黑只有一种方案
        if k == n * n {
            return 1;
        }

        // 预处理组合数 C(n, i)
        let comb = Self::build_comb(n);

        let mut ans = 0i64;
        // i 表示涂黑的行数，j 表示涂黑的列数
        // 黑色格子总数 = i*n + j*n - i*j（行与列的交集被重复计算）
        for i in 0..=n {
            for j in 0..=n {
                let sum = i * n + j * n - i * j;
                if sum == k {
                    ans += comb[i as usize] * comb[j as usize];
                }
            }
        }

        ans as i32
    }

    // 用杨辉三角预处理 C(n, k)，避免重复计算和溢出
    fn build_comb(n: i32) -> Vec<i64> {
        let n = n as usize;
        // dp[k] = C(n, k)
        let mut dp = vec![0i64; n + 1];
        dp[0] = 1;
        for i in 1..=n {
            // 从后往前更新，避免覆盖
            for j in (1..=i).rev() {
                dp[j] += dp[j - 1];
            }
        }
        dp
    }
}
```
