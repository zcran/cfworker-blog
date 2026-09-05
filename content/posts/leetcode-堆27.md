---
title: "leetcode-堆27"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 可以到达的最远建筑

给你一个整数数组 heights ，表示建筑物的高度。另有一些砖块 bricks 和梯子 ladders 。

你从建筑物 0 开始旅程，不断向后面的建筑物移动，期间可能会用到砖块或梯子。

当从建筑物 i 移动到建筑物 i+1（下标 从 0 开始 ）时：

如果当前建筑物的高度 大于或等于 下一建筑物的高度，则不需要梯子或砖块

如果当前建筑的高度 小于 下一个建筑的高度，您可以使用 一架梯子 或 (h[i+1] - h[i]) 个砖块

如果以最佳方式使用给定的梯子和砖块，返回你可以到达的最远建筑物的下标（下标 从 0 开始 ）。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn furthest_building(heights: Vec<i32>, mut bricks: i32, ladders: i32) -> i32 {
        let n = heights.len();
        // Min-heap: the smallest jump currently covered by a ladder.
        // We always keep the `ladders` largest jumps as ladders;
        // anything smaller gets kicked out to bricks.
        let mut heap: BinaryHeap<Reverse<i32>> = BinaryHeap::new();

        for i in 0..n - 1 {
            let diff = heights[i + 1] - heights[i];
            if diff <= 0 {
                continue;
            }

            heap.push(Reverse(diff));
            if heap.len() as i32 > ladders {
                // Use bricks for the smallest jump instead of a ladder
                bricks -= heap.pop().unwrap().0;
                if bricks < 0 {
                    return i as i32;
                }
            }
        }

        (n - 1) as i32
    }
}
```
