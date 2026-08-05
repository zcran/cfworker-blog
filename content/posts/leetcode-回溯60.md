---
title: "leetcode-回溯60"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 射箭比赛中的最大得分

Alice 和 Bob 是一场射箭比赛中的对手。比赛规则如下：

1. Alice 先射 numArrows 支箭，然后 Bob 也射 numArrows 支箭。

2. 分数按下述规则计算：
    1. 箭靶有若干整数计分区域，范围从 0 到 11 （含 0 和 11）。
    2. 箭靶上每个区域都对应一个得分 k（范围是 0 到 11），Alice 和 Bob 分别在得分 k 区域射中 ak 和 bk 支箭。如果 ak >= bk ，那么 Alice 得 k 分。如果 ak < bk ，则 Bob 得 k 分
    3. 如果 ak == bk == 0 ，那么无人得到 k 分。

· 例如，Alice 和 Bob 都向计分为 11 的区域射 2 支箭，那么 Alice 得 11 分。如果 Alice 向计分为 11 的区域射 0 支箭，但 Bob 向同一个区域射 2 支箭，那么 Bob 得 11 分。

给你整数 numArrows 和一个长度为 12 的整数数组 aliceArrows ，该数组表示 Alice 射中 0 到 11 每个计分区域的箭数量。现在，Bob 想要尽可能 最大化 他所能获得的总分。

返回数组 bobArrows ，该数组表示 Bob 射中 0 到 11 每个 计分区域的箭数量。且 bobArrows 的总和应当等于 numArrows 。

如果存在多种方法都可以使 Bob 获得最大总分，返回其中 任意一种 即可。


暴力更优
```
impl Solution {
    pub fn maximum_bob_points(num_arrows: i32, alice_arrows: Vec<i32>) -> Vec<i32> {
        let mut best_ans = vec![0; 12];
        let mut best_score = 0;

        // 枚举所有可能的得分区域组合（0~11共12个区域）
        for mask in 0..(1 << 12) {
            let mut used_arrows = 0;
            let mut score = 0;

            // 计算当前组合需要的箭数和得分
            for i in 0..12 {
                if (mask >> i) & 1 == 1 {
                    used_arrows += alice_arrows[i] + 1;
                    score += i;
                }
            }

            // 检查是否可行且更优
            if used_arrows <= num_arrows && score > best_score {
                best_score = score;

                // 构造答案
                let mut ans = vec![0; 12];
                for i in 0..12 {
                    if (mask >> i) & 1 == 1 {
                        ans[i] = alice_arrows[i] + 1;
                    }
                }
                // 剩余的箭全部给0分区域（不影响得分）
                ans[0] += num_arrows - used_arrows;
                best_ans = ans;
            }
        }

        best_ans
    }
}
```
