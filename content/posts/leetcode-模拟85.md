---
title: "leetcode-模拟85"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 从数量最多的堆取走礼物

给你一个整数数组 gifts ，表示各堆礼物的数量。每一秒，你需要执行以下操作：

选择礼物数量最多的那一堆。
如果不止一堆都符合礼物数量最多，从中选择任一堆即可。
将堆中的礼物数量减少到堆中原来礼物数量的平方根，向下取整。

返回在 k 秒后剩下的礼物数量。


```
use std::collections::BinaryHeap;

impl Solution {
    pub fn pick_gifts(gifts: Vec<i32>, k: i32) -> i64 {
        let mut heap = BinaryHeap::from(gifts);

        for _ in 0..k {
            // 取最大值，若<=1则后续操作无意义，提前终止
            let max = match heap.pop() {
                Some(v) if v > 1 => v,
                // 关键修复：当 max <= 1 时，要把它塞回去，否则这堆礼物就丢了
                Some(v) => { heap.push(v); break; }
                None => break,
            };
            heap.push(max.isqrt());
        }

        heap.into_iter().map(|v| v as i64).sum()
    }
}
```
