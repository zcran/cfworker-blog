---
title: "leetcode-博弈13"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 你可以获得的最大硬币数目

有 3n 堆数目不一的硬币，你和你的朋友们打算按以下方式分硬币：

每一轮中，你将会选出 任意 3 堆硬币（不一定连续）。
Alice 将会取走硬币数量最多的那一堆。
你将会取走硬币数量第二多的那一堆。
Bob 将会取走最后一堆。
重复这个过程，直到没有更多硬币。
给你一个整数数组 piles ，其中 piles[i] 是第 i 堆中硬币的数目。

返回你可以获得的最大硬币数目。


```
impl Solution {
    /// 计算你能获得的最大硬币数（LeetCode 1561. Maximum Number of Coins You Can Get）
    /// 策略：将 piles 降序排序后，每 3 个一组，你总是取每组中第二大的硬币。
    /// 因此排序后，你取的硬币位于索引 1, 3, 5, ... 且只取前 n 组（n = piles.len() / 3）。
    pub fn max_coins(mut piles: Vec<i32>) -> i32 {
        // 1. 对数组进行不稳定排序（升序），效率高，不保留相等元素的相对顺序
        piles.sort_unstable();

        // 2. 反转数组，使其变为降序排列（最大的在前）
        piles.reverse();

        // 3. 计算组数：总共有 piles.len() / 3 组，每组 3 堆，你只取每组中的第二堆
        let n = piles.len() / 3;

        // 4. 使用迭代器链式操作：
        //    - into_iter()：获取所有权，避免复制
        //    - skip(1)：跳过第一个元素（每组中的最大值，会被 Alice 拿走）
        //    - step_by(2)：从跳过后的第一个开始，每 2 个取一个（即取索引 1,3,5,...）
        //    - take(n)：只取前 n 个（因为只有 n 组）
        //    - sum::<i32>()：求和，返回你获得的总硬币数
        piles.into_iter()
             .skip(1)
             .step_by(2)
             .take(n)
             .sum::<i32>()
    }
}
```
