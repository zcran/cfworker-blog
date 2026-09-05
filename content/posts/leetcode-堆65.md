---
title: "leetcode-堆65"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最优激活顺序得到的最大总和

给你两个长度为 n 的整数数组 value 和 limit。

初始时，所有元素都是 非活跃 的。你可以按任意顺序激活它们。

要激活一个非活跃元素 i，当前 活跃元素的数量必须 严格小于 limit[i]。
当你激活元素 i 时，它的 value[i] 会被加到 总和 中（即所有进行过激活操作的元素 value[i] 之和）。
每次激活后，如果 当前 活跃元素的数量变为 x，那么 所有 满足 limit[j] <= x 的元素 j 都会永久变为非活跃状态，即使它们已经处于活跃状态。

返回通过最优选择激活顺序可以获得的 最大总和 。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_total(value: Vec<i32>, limit: Vec<i32>) -> i64 {
        let mut items: Vec<_> = value.into_iter().zip(limit.into_iter()).collect();
        items.sort_unstable_by(|a, b| {
            a.1.cmp(&b.1).then_with(|| b.0.cmp(&a.0))
        });

        let mut alive = 0usize;      // 当前活跃元素数
        let mut max_reached = 0usize; // 历史最大活跃数（报废前）
        let mut cnt = HashMap::new(); // 各 limit 对应的活跃元素个数
        let mut res = 0i64;

        for (val, lim) in items {
            // 若历史峰值已覆盖该 limit，说明此元素已永久报废
            if lim as usize <= max_reached {
                continue;
            }

            res += val as i64;
            alive += 1;
            *cnt.entry(lim).or_insert(0usize) += 1;
            max_reached = max_reached.max(alive);

            // 报废所有 limit 恰好等于当前活跃数的元素
            if let Some(c) = cnt.remove(&(alive as i32)) {
                alive -= c;
            }
        }

        res
    }
}
```
