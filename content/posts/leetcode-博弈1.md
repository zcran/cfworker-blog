---
title: "leetcode-博弈1"
date: 2026-04-09T20:51:30+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 猜数字大小 II

我们正在玩一个猜数游戏，游戏规则如下：

1.我从 1 到 n 之间选择一个数字。
2.你来猜我选了哪个数字。
3.如果你猜到正确的数字，就会 赢得游戏 。
4.如果你猜错了，那么我会告诉你，我选的数字比你的 更大或者更小 ，并且你需要继续猜数。
5.每当你猜了数字 x 并且猜错了的时候，你需要支付金额为 x 的现金。如果你花光了钱，就会 输掉游戏 。

给你一个特定的数字 n ，返回能够 确保你获胜 的最小现金数，不管我选择那个数字 。


```
impl Solution {
    pub fn get_money_amount(n: i32) -> i32 {
        let n = n as usize;
        // dp[l][r] = 在区间 [l, r] 中保证获胜的最小花费
        let mut dp = vec![vec![0; n + 2]; n + 2];  // 多分配一列，避免边界检查

        // 按区间长度从小到大计算
        for length in 1..n {               // length = 区间长度 - 1
            for l in 1..=n - length {      // l = 区间左端点
                let r = l + length;         // r = 区间右端点

                // 第一次猜的数字 v 从区间中点附近开始尝试（优化）
                let start_v = l + length / 2;
                dp[l][r] = (start_v..=r)
                    .map(|v| {
                        let cost = v as i32;
                        let left_cost = dp[l][v - 1];
                        // 当 v == r 时，右区间为空，花费 0
                        let right_cost = if v + 1 <= r { dp[v + 1][r] } else { 0 };
                        cost + left_cost.max(right_cost)
                    })
                    .min()
                    .unwrap_or(0);
            }
        }

        dp[1][n]
    }
}
```


1.扩大 DP 数组：vec![vec![0; n + 2]; n + 2]，给索引 n+1 留出空间（虽然用不到，但更安全）


2.添加边界检查：

 / / /
let right_cost = if v + 1 <= r { dp[v + 1][r] } else { 0 };
 / / /

当 v == r 时，v+1 > r，右区间为空，花费为 0
这样既保持了函数式风格（map + min），又避免了索引越界问题。
