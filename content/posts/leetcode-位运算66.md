---
title: "leetcode-位运算66"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 执行操作可获得的最大总奖励 II

给你一个整数数组 rewardValues，长度为 n，代表奖励的值。

最初，你的总奖励 x 为 0，所有下标都是 未标记 的。你可以执行以下操作 任意次 ：

从区间 [0, n - 1] 中选择一个 未标记 的下标 i。
如果 rewardValues[i] 大于 你当前的总奖励 x，则将 rewardValues[i] 加到 x 上（即 x = x + rewardValues[i]），并 标记 下标 i。

以整数形式返回执行最优操作能够获得的 最大 总奖励。


```
impl Solution {
    pub fn max_total_reward(mut reward_values: Vec<i32>) -> i32 {
        // 排序 + 去重：相同值选两次无意义（第二个必不大于当前 x）
        reward_values.sort_unstable();
        reward_values.dedup();

        let max = *reward_values.last().unwrap();
        // 总奖励上限为 max + (max - 1) = 2*max - 1，此处留一个额外位置方便处理
        let n = (2 * max) as usize;
        let mut dp = vec![false; n + 1];
        dp[0] = true;

        // 边界检查：v < 2*max，因此 x < 2*v <= 2*max，安全
        // 逆序遍历避免同一元素被重复使用
        let dp_ptr = dp.as_mut_ptr();
        for v in reward_values {
            let v = v as usize;
            for x in (v..v << 1).rev() {
                unsafe {
                    // 选中 v 的前提是当前总奖励 x - v < v，即 x < 2v
                    *dp_ptr.add(x) |= *dp_ptr.add(x - v);
                }
            }
        }

        // 找到最大的可达总奖励
        dp.iter().rposition(|&b| b).unwrap() as i32
    }
}
```
