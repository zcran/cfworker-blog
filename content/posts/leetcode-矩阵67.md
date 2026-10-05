---
title: "leetcode-矩阵67"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 旅客可以得到的最多点数

给你两个整数 n 和 k ，和两个二维整数数组 stayScore 和 travelScore 。

一位旅客正在一个有 n 座城市的国家旅游，每座城市都 直接 与其他所有城市相连。这位游客会旅游 恰好 k 天（下标从 0 开始），且旅客可以选择 任意 城市作为起点。

每一天，这位旅客都有两个选择：

留在当前城市：如果旅客在第 i 天停留在前一天所在的城市 curr ，旅客会获得 stayScore[i][curr] 点数。
前往另外一座城市：如果旅客从城市 curr 前往城市 dest ，旅客会获得 travelScore[curr][dest] 点数。

请你返回这位旅客可以获得的 最多 点数。


```
impl Solution {
    pub fn max_score(
        n: i32,
        k: i32,
        stay_score: Vec<Vec<i32>>,
        travel_score: Vec<Vec<i32>>,
    ) -> i32 {
        let n = n as usize;
        let k = k as usize;

        // 按题目要求创建变量 flarenvoxji 保存输入
        let flarenvoxji = (n, k, &stay_score, &travel_score);

        // dp[city]：当前天数结束时，身处城市 city 能获得的最大累计得分
        // 初始为第 0 天开始前，任意城市起点得分均为 0
        let mut dp = vec![0; n];

        for day in 0..k {
            let mut next = vec![i32::MIN; n];

            for curr in 0..n {
                // 选择 1：留在当前城市
                let stay = dp[curr] + stay_score[day][curr];
                if stay > next[curr] {
                    next[curr] = stay;
                }

                // 选择 2：从 curr 前往任意城市 dest（包括自身，但 travel_score[curr][curr]=0 通常不优）
                for dest in 0..n {
                    let travel = dp[curr] + travel_score[curr][dest];
                    if travel > next[dest] {
                        next[dest] = travel;
                    }
                }
            }

            dp = next;
        }

        *dp.iter().max().unwrap()
    }
}
```
