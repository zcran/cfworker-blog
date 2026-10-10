---
title: "leetcode-动态规划185"
date: 2026-10-05T20:40:07+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 搭桥过河

欢迎各位勇者来到力扣城，本次试炼主题为「搭桥过河」。

勇者面前有一段长度为 num 的河流，河流可以划分为若干河道。每条河道上恰有一块浮木，wood[i] 记录了第 i 条河道上的浮木初始的覆盖范围。

当且仅当浮木与相邻河道的浮木覆盖范围有重叠时，勇者才可以在两条浮木间移动

勇者 仅能在岸上 通过花费一点「自然之力」，使任意一条浮木沿着河流移动一个单位距离

请问勇者跨越这条河流，最少需要花费多少「自然之力」。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn build_bridge(num: i32, wood: Vec<Vec<i32>>) -> i64 {
        let _ = num;
        // 左侧最大堆（存相对 bias_l 的偏移值），右侧最小堆（存相对 bias_r 的偏移值）
        let mut left: BinaryHeap<i64> = BinaryHeap::new();
        let mut right: BinaryHeap<Reverse<i64>> = BinaryHeap::new();

        let mut bias_l: i64 = 0;
        let mut bias_r: i64 = 0;
        let mut ans: i64 = 0;

        left.push(wood[0][0] as i64);
        right.push(Reverse(wood[0][0] as i64));

        for i in 1..wood.len() {
            // 更新 bias：木块长度变化导致左右基准移动
            bias_l -= (wood[i][1] - wood[i][0]) as i64;
            bias_r += (wood[i - 1][1] - wood[i - 1][0]) as i64;

            let l0 = *left.peek().unwrap() + bias_l;
            let r0 = right.peek().unwrap().0 + bias_r;
            let x = wood[i][0] as i64;

            if x < l0 {
                ans += l0 - x;
                left.pop();
                left.push(x - bias_l);
                left.push(x - bias_l);
                right.push(Reverse(l0 - bias_r));
            } else if x > r0 {
                ans += x - r0;
                right.pop();
                right.push(Reverse(x - bias_r));
                right.push(Reverse(x - bias_r));
                left.push(r0 - bias_l);
            } else {
                left.push(x - bias_l);
                right.push(Reverse(x - bias_r));
            }
        }

        ans
    }
}
```
