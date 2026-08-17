---
title: "leetcode-模拟35"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 圆形赛道上经过次数最多的扇区

给你一个整数 n 和一个整数数组 rounds 。有一条圆形赛道由 n 个扇区组成，扇区编号从 1 到 n 。现将在这条赛道上举办一场马拉松比赛，该马拉松全程由 m 个阶段组成。其中，第 i 个阶段将会从扇区 rounds[i - 1] 开始，到扇区 rounds[i] 结束。举例来说，第 1 阶段从 rounds[0] 开始，到 rounds[1] 结束。

请你以数组形式返回经过次数最多的那几个扇区，按扇区编号 升序 排列。

注意，赛道按扇区编号升序逆时针形成一个圆（请参见第一个示例）。


```
impl Solution {
    pub fn most_visited(n: i32, rounds: Vec<i32>) -> Vec<i32> {
        let (start, end) = (*rounds.first().unwrap(), *rounds.last().unwrap());

        // 完整跑一圈所有扇区次数+1，相互抵消。
        // 因此只需关注从 start 到 end 的净路径。
        if start <= end {
            (start..=end).collect()
        } else {
            (1..=end).chain(start..=n).collect()
        }
    }
}
```
