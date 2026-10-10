---
title: "leetcode-动态规划140"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计能获胜的出招序列数

Alice 和 Bob 正在玩一个幻想战斗游戏，游戏共有 n 回合，每回合双方各自都会召唤一个魔法生物：火龙（F）、水蛇（W）或地精（E）。每回合中，双方 同时 召唤魔法生物，并根据以下规则得分：

如果一方召唤火龙而另一方召唤地精，召唤 火龙 的玩家将获得一分。
如果一方召唤水蛇而另一方召唤火龙，召唤 水蛇 的玩家将获得一分。
如果一方召唤地精而另一方召唤水蛇，召唤 地精 的玩家将获得一分。
如果双方召唤相同的生物，那么两个玩家都不会获得分数。

给你一个字符串 s，包含 n 个字符 'F'、'W' 和 'E'，代表 Alice 每回合召唤的生物序列：

如果 s[i] == 'F'，Alice 召唤火龙。
如果 s[i] == 'W'，Alice 召唤水蛇。
如果 s[i] == 'E'，Alice 召唤地精。


Bob 的出招序列未知，但保证 Bob 不会在连续两个回合中召唤相同的生物。如果在 n 轮后 Bob 获得的总分 严格大于 Alice 的总分，则 Bob 战胜 Alice。

返回 Bob 可以用来战胜 Alice 的不同出招序列的数量。

由于答案可能非常大，请返回答案对 10^9 + 7 取余 后的结果。


```
impl Solution {
    pub fn count_winning_sequences(s: String) -> i32 {
        const MOD: i32 = 1_000_000_007;

        // 对 Alice 的每个生物，返回 Bob 三种出招的得分：
        // 顺序固定为 [F, W, E]，得分 ∈ {-1, 0, 1}
        fn score(alice: u8) -> [i32; 3] {
            match alice {
                b'F' => [0, 1, -1],  // Bob: F=平, W=胜, E=负
                b'W' => [-1, 0, 1],  // Bob: F=负, W=平, E=胜
                _    => [1, -1, 0],  // b'E': Bob: F=胜, W=负, E=平
            }
        }

        let n = s.len();
        let bytes = s.as_bytes();

        // 得分范围 [-n, n]，加偏移 n+1 使其非负
        let size = 2 * n + 3;
        let offset = (n + 1) as i32;

        // dp[last][score_idx]：上一回合 Bob 出招为 last、当前得分差为 score_idx 的方案数
        // last: 0=F, 1=W, 2=E
        let mut dp = vec![[0i32; 3]; size];

        // 第一回合初始化
        let sc = score(bytes[0]);
        for b in 0..3 {
            let idx = (sc[b] + offset) as usize;
            dp[idx][b] = 1;
        }

        // 逐回合递推
        for i in 1..n {
            let sc = score(bytes[i]);
            let mut ndp = vec![[0i32; 3]; size];

            for idx in 0..size {
                let row = &dp[idx];
                if row[0] == 0 && row[1] == 0 && row[2] == 0 {
                    continue;
                }

                for b in 0..3 {
                    let s_b = sc[b];
                    let new_idx = (idx as i32 + s_b) as usize;

                    // Bob 不能连续两回合出相同生物
                    let sum = match b {
                        0 => (row[1] + row[2]) % MOD,
                        1 => (row[0] + row[2]) % MOD,
                        _ => (row[0] + row[1]) % MOD,
                    };

                    ndp[new_idx][b] = (ndp[new_idx][b] + sum) % MOD;
                }
            }

            dp = ndp;
        }

        // 统计得分差严格大于 0（即 idx > offset）的所有方案
        let mut ans = 0i32;
        for idx in (offset as usize + 1)..size {
            for b in 0..3 {
                ans = (ans + dp[idx][b]) % MOD;
            }
        }

        ans
    }
}
```
