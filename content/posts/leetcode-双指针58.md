---
title: "leetcode-双指针58"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 令牌放置

你的初始 能量 为 power，初始 分数 为 0，只有一包令牌以整数数组 tokens 给出。其中 tokens[i] 是第 i 个令牌的值（下标从 0 开始）。

你的目标是通过有策略地使用这些令牌以 最大化 总 分数。在一次行动中，你可以用两种方式中的一种来使用一个 未被使用的 令牌（但不是对同一个令牌使用两种方式）：

朝上：如果你当前 至少 有 tokens[i] 点 能量 ，可以使用令牌 i ，失去 tokens[i] 点 能量 ，并得到 1 分 。
朝下：如果你当前至少有 1 分 ，可以使用令牌 i ，获得 tokens[i] 点 能量 ，并失去 1 分 。

在使用 任意 数量的令牌后，返回我们可以得到的最大 分数 。


```
impl Solution {
    pub fn bag_of_tokens_score(mut tokens: Vec<i32>, mut power: i32) -> i32 {
        tokens.sort_unstable();

        let mut score = 0;
        let mut max_score = 0;
        let mut left = 0;

        // 处理空数组
        if tokens.is_empty() {
            return 0;
        }

        let mut right = tokens.len() - 1;

        // 贪心策略：能量充足时尽量得分（朝上），能量不足时用分数换能量（朝下）
        while left <= right {
            if power >= tokens[left] {
                // 能量充足，用最小能量换 1 分
                power -= tokens[left];
                score += 1;
                left += 1;
                max_score = max_score.max(score);
            } else if score > 0 {
                // 能量不足，用 1 分换最大能量
                power += tokens[right];
                score -= 1;
                right -= 1;
            } else {
                // 既无足够能量，也无可用的分数
                break;
            }
        }

        max_score
    }
}
```
