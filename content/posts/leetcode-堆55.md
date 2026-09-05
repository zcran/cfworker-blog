---
title: "leetcode-堆55"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 老鼠和奶酪

有两只老鼠和 n 块不同类型的奶酪，每块奶酪都只能被其中一只老鼠吃掉。

下标为 i 处的奶酪被吃掉的得分为：

如果第一只老鼠吃掉，则得分为 reward1[i] 。
如果第二只老鼠吃掉，则得分为 reward2[i] 。

给你一个正整数数组 reward1 ，一个正整数数组 reward2 ，和一个非负整数 k 。

请你返回第一只老鼠恰好吃掉 k 块奶酪的情况下，最大 得分为多少。


```
impl Solution {
    pub fn mice_and_cheese(reward1: Vec<i32>, reward2: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;

        // 先假设第二只老鼠吃掉所有奶酪，总得分 = sum(reward2)
        let base: i32 = reward2.iter().sum();

        // 计算把每块奶酪从第二只改给第一只的"收益变化"
        // 选 k 个最大的差值加到 base 上
        let mut diff: Vec<i32> = reward1
            .into_iter()
            .zip(reward2.into_iter())
            .map(|(a, b)| a - b)
            .collect();

        // 降序排列，取前 k 个最大的差值
        diff.sort_unstable_by(|a, b| b.cmp(a));

        base + diff.iter().take(k).sum::<i32>()
    }
}
```
