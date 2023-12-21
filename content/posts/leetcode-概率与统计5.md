---
title: "Leetcode 概率与统计5"
date: 2023-12-19T15:47:55+08:00
tags: ["leetcode", "概率与统计"]
draft: false
---

## N个骰子的点数

你选择掷出 num 个色子，请返回所有点数总和的概率。

你需要用一个浮点数数组返回答案，其中第 i 个元素代表这 num 个骰子所能掷出的点数集合中第 i 小的那个的概率。

```
impl Solution {
    pub fn dices_probability(n: i32) -> Vec<f64> {
        let mut ans = vec![1.0 / 6.0].repeat(6);
        for i in 2..=n as usize {
            let mut tmp = vec![0.0; 5 * i + 1];
            for j in 0..6 {
                for k in 0..ans.len() {
                    tmp[k + j] += ans[k] / 6.0;
                }
            }
            ans = tmp;
        }
        ans
    }
}
```