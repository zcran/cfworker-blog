---
title: "leetcode-前缀和137"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 在下标间移动的最小代价

给你一个整数数组 nums，nums 是 严格递增 的。

对于每个下标 x，设 closest(x) 为使得 abs(nums[x] - nums[y]) 最小化 的 相邻 下标 y。如果两个 相邻 下标的差值相同，则选择 较小 的下标。

从任意下标 x 出发，你可以通过以下两种方式移动：

移动到任意下标 y，代价为 abs(nums[x] - nums[y])，或者
移动到 closest(x)，代价为 1。

同时给你一个二维整数数组 queries，其中每个 queries[i] = [li, ri]。

对于每个查询，计算从下标 li 移动到下标 ri 的 最小总代价。

返回一个整数数组 ans，其中 ans[i] 是第 i 个查询的答案。

两个值 x 和 y 之间的 绝对差 定义为 abs(x - y)。


```
impl Solution {
    pub fn min_cost(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = nums.len();

        // 1. 计算每个位置的 closest 邻居
        // closest[i] 表示从 i 出发代价为 1 能到达的邻居下标
        let mut closest = vec![0usize; n];
        closest[0] = 1;
        closest[n - 1] = n - 2;
        for i in 1..n - 1 {
            // 选择距离更近的邻居，若相等则选较小的下标
            if nums[i] - nums[i - 1] <= nums[i + 1] - nums[i] {
                closest[i] = i - 1;
            } else {
                closest[i] = i + 1;
            }
        }

        // 2. 构建从左到右和从右到左的前缀代价
        // right_cost[i] 表示从 0 到 i 的累积最小代价（向右移动）
        let mut right_cost = vec![0i64; n];
        for i in 0..n - 1 {
            // 从 i 到 i+1 的最小代价：
            // - 若 closest[i] == i+1，则可用代价 1 直接到达
            // - 否则需要支付数值差
            let cost = if closest[i] == i + 1 { 1 } else { (nums[i + 1] - nums[i]) as i64 };
            right_cost[i + 1] = right_cost[i] + cost;
        }

        // left_cost[i] 表示从 n-1 到 i 的累积最小代价（向左移动）
        let mut left_cost = vec![0i64; n];
        for i in (1..n).rev() {
            // 从 i 到 i-1 的最小代价：
            // - 若 closest[i] == i-1，则可用代价 1 直接到达
            // - 否则需要支付数值差
            let cost = if closest[i] == i - 1 { 1 } else { (nums[i] - nums[i - 1]) as i64 };
            left_cost[i - 1] = left_cost[i] + cost;
        }

        // 3. 回答所有查询
        queries
            .iter()
            .map(|q| {
                let (l, r) = (q[0] as usize, q[1] as usize);
                let cost = if l < r {
                    right_cost[r] - right_cost[l]
                } else {
                    left_cost[r] - left_cost[l]
                };
                cost as i32
            })
            .collect()
    }
}
```
