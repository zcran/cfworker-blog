---
title: "leetcode-单调队列14"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 购买水果需要的最少金币数

给你一个 下标从 0 开始的 整数数组 prices ，其中 prices[i] 表示你购买第 i + 1 个水果需要花费的金币数目。

水果超市有如下促销活动：

如果你花费 prices[i] 购买了下标为 i + 1 的水果，那么你可以免费获得下标范围在 [i + 1, i + i] 的水果。
注意 ，即使你 可以 免费获得水果 j ，你仍然可以花费 prices[j - 1] 个金币去购买它以获得它的奖励。

请你返回获得所有水果所需要的 最少 金币数。

```
use std::collections::VecDeque;

impl Solution {
    pub fn minimum_coins(prices: Vec<i32>) -> i32 {
        let n = prices.len();
        let mut dp = vec![i32::MAX; n + 1];
        dp[0] = 0;

        // 单调队列：存储索引 i，维护 dp[i] + prices[i] 的单调递增
        let mut queue: VecDeque<usize> = VecDeque::new();

        for i in 0..=n {
            // 1. 移除无法覆盖当前位置的索引
            // 如果购买第 j+1 个水果，能覆盖到 [j+1, 2*(j+1)] 即 [j+1, 2j+2]
            // 要覆盖到 i（前 i 个水果），需要 i <= 2j+2，即 j >= ceil((i-2)/2)
            while let Some(&j) = queue.front() {
                if j + j + 2 < i {  // 2*(j+1) < i
                    queue.pop_front();
                } else {
                    break;
                }
            }

            // 2. 从队列中获取最优值更新 dp[i]
            if let Some(&best_j) = queue.front() {
                dp[i] = dp[i].min(dp[best_j] + prices[best_j]);
            }

            // 3. 将当前位置加入队列（为后续位置做准备）
            if i < n {
                let current_cost = dp[i] + prices[i];
                while let Some(&last) = queue.back() {
                    let last_cost = dp[last] + prices[last];
                    if last_cost >= current_cost {
                        queue.pop_back();
                    } else {
                        break;
                    }
                }
                queue.push_back(i);
            }
        }

        dp[n]
    }
}
```
