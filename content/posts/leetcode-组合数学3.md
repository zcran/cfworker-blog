---
title: "leetcode-组合数学3"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 播放列表的数量


你的音乐播放器里有 n 首不同的歌，在旅途中，你计划听 goal 首歌（不一定不同，即，允许歌曲重复）。你将会按如下规则创建播放列表：

每首歌 至少播放一次 。
一首歌只有在其他 k 首歌播放完之后才能再次播放。

给你 n、goal 和 k ，返回可以满足要求的播放列表的数量。由于答案可能非常大，请返回对 109 + 7 取余 的结果。

```
impl Solution {
    pub fn num_music_playlists(n: i32, goal: i32, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as usize;
        let goal = goal as usize;
        let k = k as usize;

        // 一维滚动数组 dp[j] 表示当前已播放长度下，包含 j 首不同歌曲的方案数
        let mut dp = vec![0_i64; n + 1];
        dp[0] = 1; // 长度 0，0 首不同歌曲的方案数为 1

        for i in 1..=goal {
            // 当前长度最多包含 min(i, n) 首不同歌曲
            let max_j = n.min(i);
            // 逆序更新，确保使用上一轮的状态（未覆盖）
            for j in (1..=max_j).rev() {
                // 1. 播放一首新歌：从剩余未选过的 n-(j-1) 首中选一首
                let mut val = dp[j - 1] * (n - j + 1) as i64 % MOD;
                // 2. 播放一首旧歌：从当前已选的 j 首歌中，排除最后 k 首（必须互异），可选 j-k 首
                if j > k {
                    val = (val + dp[j] * (j - k) as i64) % MOD;
                }
                dp[j] = val;
            }
            // 长度大于 0 时不可能有 0 首不同歌曲，置零供下一轮使用
            dp[0] = 0;
        }

        dp[n] as i32
    }
}
```
