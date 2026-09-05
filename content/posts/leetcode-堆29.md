---
title: "leetcode-堆29"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 吃苹果的最大数目

有一棵特殊的苹果树，一连 n 天，每天都可以长出若干个苹果。在第 i 天，树上会长出 apples[i] 个苹果，这些苹果将会在 days[i] 天后（也就是说，第 i + days[i] 天时）腐烂，变得无法食用。也可能有那么几天，树上不会长出新的苹果，此时用 apples[i] == 0 且 days[i] == 0 表示。

你打算每天 最多 吃一个苹果来保证营养均衡。注意，你可以在这 n 天之后继续吃苹果。

给你两个长度为 n 的整数数组 days 和 apples ，返回你可以吃掉的苹果的最大数目。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn eaten_apples(apples: Vec<i32>, days: Vec<i32>) -> i32 {
        let mut eaten = 0;
        let mut heap: BinaryHeap<Reverse<(i32, i32)>> = BinaryHeap::new();
        let n = apples.len();

        for i in 0..n {
            let day = i as i32;
            // Discard rotten apples
            while let Some(&Reverse((rot, _))) = heap.peek() {
                if rot > day { break; }
                heap.pop();
            }
            // Add newly grown apples
            if apples[i] > 0 {
                heap.push(Reverse((day + days[i], apples[i])));
            }
            // Eat the apple that rots the soonest
            if let Some(Reverse((rot, mut cnt))) = heap.pop() {
                cnt -= 1;
                if cnt > 0 {
                    heap.push(Reverse((rot, cnt)));
                }
                eaten += 1;
            }
        }

        // After the growing period, eat remaining apples
        let mut day = n as i32;
        while let Some(Reverse((rot, cnt))) = heap.pop() {
            if rot <= day { continue; }
            let can_eat = std::cmp::min(rot - day, cnt);
            eaten += can_eat;
            day += can_eat;
        }

        eaten
    }
}
```
