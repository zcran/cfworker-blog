---
title: "leetcode-滑动窗口64"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 两个线段获得的最多奖品

在 X轴 上有一些奖品。给你一个整数数组 prizePositions ，它按照 非递减 顺序排列，其中 prizePositions[i] 是第 i 件奖品的位置。数轴上一个位置可能会有多件奖品。再给你一个整数 k 。

你可以同时选择两个端点为整数的线段。每个线段的长度都必须是 k 。你可以获得位置在任一线段上的所有奖品（包括线段的两个端点）。注意，两个线段可能会有相交。

比方说 k = 2 ，你可以选择线段 [1, 3] 和 [2, 4] ，你可以获得满足 1 <= prizePositions[i] <= 3 或者 2 <= prizePositions[i] <= 4 的所有奖品 i 。

请你返回在选择两个最优线段的前提下，可以获得的 最多 奖品数目。


```
impl Solution {
    pub fn maximize_win(prize_positions: Vec<i32>, k: i32) -> i32 {
        let n = prize_positions.len();
        // dp[i] 表示前 i 个奖品中，一条线段最多能覆盖的数量
        let mut dp = vec![0; n + 1];
        let mut ans = 0;
        let mut left = 0; // 滑动窗口左边界，避免每次二分查找

        for right in 0..n {
            // 维护窗口 [left, right] 使得 prize_positions[right] - prize_positions[left] <= k
            while prize_positions[right] - prize_positions[left] > k {
                left += 1;
            }

            let cur_len = right - left + 1;
            // 当前线段覆盖 [left, right]，另一条线段在前 left 个奖品中取最优
            ans = ans.max(cur_len + dp[left]);
            // 更新 dp，dp[right + 1] 表示前 right + 1 个奖品中一条线段的最大覆盖数
            dp[right + 1] = dp[right].max(cur_len);
        }

        ans as i32
    }
}
```
