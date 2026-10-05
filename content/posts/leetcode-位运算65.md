---
title: "leetcode-位运算65"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 执行操作可获得的最大总奖励 I

给你一个整数数组 rewardValues，长度为 n，代表奖励的值。

最初，你的总奖励 x 为 0，所有下标都是 未标记 的。你可以执行以下操作 任意次 ：

从区间 [0, n - 1] 中选择一个 未标记 的下标 i。
如果 rewardValues[i] 大于 你当前的总奖励 x，则将 rewardValues[i] 加到 x 上（即 x = x + rewardValues[i]），并 标记 下标 i。

以整数形式返回执行最优操作能够获得的 最大 总奖励。


```
impl Solution {
    pub fn max_total_reward(mut reward_values: Vec<i32>) -> i32 {
        // 排序后，每个值若能选，必然是当前 x 小于它时才可选
        reward_values.sort();
        let m = *reward_values.last().unwrap() as usize;

        // dp[k] 表示总奖励 k 是否可达
        // 可达总奖励最大值不超过 2*m - 1（最后一个值 m 加上之前最多 m-1）
        let mut dp = vec![false; 2 * m];
        dp[0] = true;

        for &x in &reward_values {
            let x = x as usize;
            // 逆序遍历避免同一元素被重复使用
            // 可转移到 [x, 2x) 区间：因为选中 x 要求当前总奖励 < x
            for k in (x..2 * x).rev() {
                if dp[k - x] {
                    dp[k] = true;
                }
            }
        }

        // 找到最大的可达总奖励
        dp.iter().rposition(|&v| v).unwrap() as i32
    }
}
```
