---
title: "leetcode-贪心158"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 生成赛程

给你一个整数 n，表示 n 支队伍。你需要生成一个赛程，使得：

· 每支队伍与其他队伍 正好比赛两次：一次在主场，一次在客场。
· 每天 只有一场 比赛；赛程是一个 连续的 天数列表，schedule[i] 表示第 i 天的比赛。
· 没有队伍在 连续 两天内进行比赛。

返回一个 2D 整数数组 schedule，其中 schedule[i][0] 表示主队，schedule[i][1] 表示客队。如果有多个满足条件的赛程，返回 其中任意一个 。

如果没有满足条件的赛程，返回空数组。


```
impl Solution {
    pub fn generate_schedule(n: i32) -> Vec<Vec<i32>> {
        // n < 5 时无法构造满足条件的赛程
        if n < 5 {
            return vec![];
        }
        let n = n as usize;
        let mut ans = Vec::with_capacity(n * (n - 1));

        // 对距离 d = 2..n-2，每天安排一轮：i 主场对阵 (i+d) % n
        for d in 2..n - 1 {
            for i in 0..n {
                ans.push(vec![i as i32, ((i + d) % n) as i32]);
            }
        }

        // 交错安排距离 d = 1 和 d = n-1，避免队伍连续两天比赛
        for i in 0..n {
            ans.push(vec![i as i32, ((i + 1) % n) as i32]);
            ans.push(vec![((i + n - 1) % n) as i32, ((i + n - 2) % n) as i32]);
        }

        ans
    }
}
```
