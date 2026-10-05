---
title: "leetcode-贪心66"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 卡车上的最大单元数

请你将一些箱子装在 一辆卡车 上。给你一个二维数组 boxTypes ，其中 boxTypes[i] = [numberOfBoxesi, numberOfUnitsPerBoxi] ：

numberOfBoxesi 是类型 i 的箱子的数量。
numberOfUnitsPerBoxi 是类型 i 每个箱子可以装载的单元数量。

整数 truckSize 表示卡车上可以装载 箱子 的 最大数量 。只要箱子数量不超过 truckSize ，你就可以选择任意箱子装到卡车上。

返回卡车可以装载 单元 的 最大 总数。


```
impl Solution {
    pub fn maximum_units(mut box_types: Vec<Vec<i32>>, mut truck_size: i32) -> i32 {
        // 按每箱单元数降序，优先装单位价值最高的箱子
        box_types.sort_unstable_by_key(|b| std::cmp::Reverse(b[1]));

        let mut total = 0;
        for b in &box_types {
            // 当前类型最多能装 min(剩余容量, 该类型箱数)
            let cnt = truck_size.min(b[0]);
            total += cnt * b[1];
            truck_size -= cnt;
            if truck_size == 0 {
                break;
            }
        }
        total
    }
}
```
