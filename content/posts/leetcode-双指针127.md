---
title: "leetcode-双指针127"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 预算下的最大总容量

给你两个长度为 n 的整数数组 costs 和 capacity，其中 costs[i] 表示第 i 台机器的购买成本，capacity[i] 表示其性能容量。

同时，给定一个整数 budget。

你可以选择 最多两台不同的机器，使得所选机器的 总成本 严格小于 budget。

返回可以实现的 最大总容量。


```
impl Solution {
    pub fn max_capacity(costs: Vec<i32>, capacity: Vec<i32>, budget: i32) -> i32 {
        // 过滤掉买不起的机器，并按价格升序排序
        let mut machines: Vec<(i32, i32)> = costs
            .into_iter()
            .zip(capacity)
            .filter(|&(cost, _)| cost < budget)
            .collect();
        machines.sort_unstable_by_key(|&(cost, _)| cost);

        let n = machines.len();
        // pre_max[i]：前 i 台机器（按价格升序）中容量的最大值
        let mut pre_max = vec![0i32; n + 1];
        let mut ans = 0;

        for i in 0..n {
            let (cost, cap) = machines[i];
            // 第一台机器价格 cost，第二台价格必须 < budget - cost
            // 在 machines[..i] 中找第一个价格 >= budget - cost 的位置 j
            let limit = budget - cost;
            let j = machines[..i].partition_point(|&(c, _)| c < limit);
            // 单台机器（pre_max[0] = 0）或两台机器组合
            ans = ans.max(cap + pre_max[j]);
            pre_max[i + 1] = pre_max[i].max(cap);
        }

        ans
    }
}
```
