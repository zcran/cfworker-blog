---
title: "leetcode-堆6"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


##  IPO

假设 力扣（LeetCode）即将开始 IPO 。为了以更高的价格将股票卖给风险投资公司，力扣 希望在 IPO 之前开展一些项目以增加其资本。 由于资源有限，它只能在 IPO 之前完成最多 k 个不同的项目。帮助 力扣 设计完成最多 k 个不同项目后得到最大总资本的方式。

给你 n 个项目。对于每个项目 i ，它都有一个纯利润 profits[i] ，和启动该项目需要的最小资本 capital[i] 。

最初，你的资本为 w 。当你完成一个项目时，你将获得纯利润，且利润将被添加到你的总资本中。

总而言之，从给定项目中选择 最多 k 个不同项目的列表，以 最大化最终资本 ，并输出最终可获得的最多资本。

答案保证在 32 位有符号整数范围内。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 每次选当前资本能启动的项目中利润最大的，重复 k 次
    pub fn find_maximized_capital(k: i32, w: i32, profits: Vec<i32>, capital: Vec<i32>) -> i32 {
        let n = profits.len();
        // 按所需资本升序排列：(capital, profit)
        let mut projects: Vec<(i32, i32)> = capital.into_iter().zip(profits).collect();
        projects.sort_unstable_by_key(|&(c, _)| c);

        let mut heap = BinaryHeap::new();
        let mut i = 0;      // 指向尚未入堆的项目
        let mut capital = w;
        let k = k as usize;

        for _ in 0..k {
            // 将所有当前资本能启动的项目利润入堆
            while i < n && projects[i].0 <= capital {
                heap.push(projects[i].1);
                i += 1;
            }
            // 选利润最大的项目，没有可做的就结束
            match heap.pop() {
                Some(p) => capital += p,
                None => break,
            }
        }

        capital
    }
}
```
