---
title: "leetcode-模拟92"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 找出转圈游戏输家

n 个朋友在玩游戏。这些朋友坐成一个圈，按 顺时针方向 从 1 到 n 编号。准确的说，从第 i 个朋友的位置开始顺时针移动 1 步会到达第 (i + 1) 个朋友的位置（1 <= i < n），而从第 n 个朋友的位置开始顺时针移动 1 步会回到第 1 个朋友的位置。

游戏规则如下：

第 1 个朋友接球。

· 接着，第 1 个朋友将球传给距离他顺时针方向 k 步的朋友。
· 然后，接球的朋友应该把球传给距离他顺时针方向 2 * k 步的朋友。
· 接着，接球的朋友应该把球传给距离他顺时针方向 3 * k 步的朋友，以此类推。

换句话说，在第 i 轮中持有球的那位朋友需要将球传递给距离他顺时针方向 i * k 步的朋友。

当某个朋友第 2 次接到球时，游戏结束。

在整场游戏中没有接到过球的朋友是 输家 。

给你参与游戏的朋友数量 n 和一个整数 k ，请按升序排列返回包含所有输家编号的数组 answer 作为答案。


```
impl Solution {
    /// LeetCode 2682: Find the Losers of the Circular Game
    ///
    /// 模拟传球过程，用 visited 数组标记接到过球的朋友。
    /// 第 i 轮（从1开始）当前持球者顺时针传 i*k 步，
    /// 当某人第二次接到球时游戏结束，返回从未接球的输家编号。
    pub fn circular_game_losers(n: i32, k: i32) -> Vec<i32> {
        let n = n as usize;
        let k = k as usize;
        let mut visited = vec![false; n];

        let mut pos = 0;      // 当前持球者位置（0-based）
        let mut round = 1;    // 当前轮次，即步数乘数

        // 模拟直到某人第二次接到球
        while !visited[pos] {
            visited[pos] = true;
            pos = (pos + round * k) % n;
            round += 1;
        }

        // 收集从未接球的输家（转回 1-based 编号）
        visited
            .into_iter()
            .enumerate()
            .filter_map(|(i, seen)| if !seen { Some(i as i32 + 1) } else { None })
            .collect()
    }
}

```
