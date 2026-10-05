---
title: "leetcode-贪心39"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 玩筹码

有 n 个筹码。第 i 个筹码的位置是 position[i] 。

我们需要把所有筹码移到同一个位置。在一步中，我们可以将第 i 个筹码的位置从 position[i] 改变为:

position[i] + 2 或 position[i] - 2 ，此时 cost = 0
position[i] + 1 或 position[i] - 1 ，此时 cost = 1

返回将所有筹码移动到同一位置上所需要的 最小代价 。


```
impl Solution {
    pub fn min_cost_to_move_chips(position: Vec<i32>) -> i32 {
        // 移动 2 步免费，因此奇偶性相同的筹码可零代价聚到一起；
        // 最终只需把奇数和偶数中较少的一类逐个移动 1 步，代价即为该类的数量
        let odd = position.iter().filter(|v| *v & 1 == 1).count();
        let even = position.len() - odd;
        odd.min(even) as i32
    }
}
```
