---
title: "leetcode-堆36"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最小未被占据椅子的编号

有 n 个朋友在举办一个派对，这些朋友从 0 到 n - 1 编号。派对里有 无数 张椅子，编号为 0 到 infinity 。当一个朋友到达派对时，他会占据 编号最小 且未被占据的椅子。

比方说，当一个朋友到达时，如果椅子 0 ，1 和 5 被占据了，那么他会占据 2 号椅子。
当一个朋友离开派对时，他的椅子会立刻变成未占据状态。如果同一时刻有另一个朋友到达，可以立即占据这张椅子。

给你一个下标从 0 开始的二维整数数组 times ，其中 times[i] = [arrivalˇi, leavingˇi] 表示第 i 个朋友到达和离开的时刻，同时给你一个整数 targetFriend 。所有到达时间 互不相同 。

请你返回编号为 targetFriend 的朋友占据的 椅子编号 。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn smallest_chair(times: Vec<Vec<i32>>, target_friend: i32) -> i32 {
        let n = times.len();
        // Events: (time, is_arrival, friend_id)
        // is_arrival=false (leave) sorts before is_arrival=true (arrive) at same time
        let mut events: Vec<(i32, bool, usize)> = Vec::with_capacity(n * 2);
        for (id, t) in times.into_iter().enumerate() {
            events.push((t[0], true, id));   // arrival
            events.push((t[1], false, id));  // leaving
        }
        events.sort_unstable();

        let mut available: BinaryHeap<Reverse<i32>> = BinaryHeap::new();
        let mut seat_of: Vec<i32> = vec![-1; n];
        let mut next_seat = 0;

        for (_, is_arrival, id) in events {
            if is_arrival {
                let seat = if let Some(Reverse(s)) = available.pop() {
                    s
                } else {
                    let s = next_seat;
                    next_seat += 1;
                    s
                };
                if id == target_friend as usize {
                    return seat;
                }
                seat_of[id] = seat;
            } else {
                available.push(Reverse(seat_of[id]));
            }
        }

        -1
    }
}
```
