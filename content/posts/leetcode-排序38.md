---
title: "leetcode-排序38"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 无矛盾的最佳球队

假设你是球队的经理。对于即将到来的锦标赛，你想组合一支总体得分最高的球队。球队的得分是球队中所有球员的分数 总和 。

然而，球队中的矛盾会限制球员的发挥，所以必须选出一支 没有矛盾 的球队。如果一名年龄较小球员的分数 严格大于 一名年龄较大的球员，则存在矛盾。同龄球员之间不会发生矛盾。

给你两个列表 scores 和 ages，其中每组 scores[i] 和 ages[i] 表示第 i 名球员的分数和年龄。请你返回 所有可能的无矛盾球队中得分最高那支的分数 。


```
impl Solution {
    /// 选出无矛盾球队（年龄升序时分数不严格递减）的最大得分和
    ///
    /// 思路：按年龄升序、年龄相同时分数升序排序。
    /// 排序后问题转化为「在分数序列上选若干元素，使分数不减且总和最大」，
    /// 即加权最长不下降子序列：dp[i] = 以 i 结尾的最大得分。
    /// 年龄相同者分数升序，保证同龄可任意组合且不会误判矛盾。
    /// 时间 O(n²)，额外空间 O(n)。
    pub fn best_team_score(scores: Vec<i32>, ages: Vec<i32>) -> i32 {
        // (分数, 年龄)；年龄升序，同龄分数升序
        let mut players: Vec<(i32, i32)> = scores.iter().copied().zip(ages).collect();
        players.sort_unstable_by(|a, b| a.1.cmp(&b.1).then_with(|| a.0.cmp(&b.0)));

        let n = players.len();
        let mut dp = vec![0i32; n];
        let mut best = 0;

        for i in 0..n {
            let score_i = players[i].0;
            dp[i] = score_i; // 至少只选自己
            for j in 0..i {
                // 年龄已保证 players[j].1 <= players[i].1；分数需不降
                if players[j].0 <= score_i {
                    dp[i] = dp[i].max(dp[j] + score_i);
                }
            }
            best = best.max(dp[i]);
        }

        best
    }
}
```
