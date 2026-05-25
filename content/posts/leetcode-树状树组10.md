---
title: "leetcode-树状树组10"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---



## 查询带键的排列

给定一个正整数数组 queries ，其取值范围在 1 到 m 之间。 请你根据以下规则按顺序处理所有 queries[i]（从 i=0 到 i=queries.length-1）：

首先，你有一个排列 P=[1,2,3,...,m]。
对于当前的 i ，找到 queries[i] 在排列 P 中的位置（从 0 开始索引），然后将它移到排列 P 的开头（即下标为 0 处）。注意， queries[i] 的查询结果是 queries[i] 在 P 中移动前的位置。
返回一个数组，包含从给定  queries 中查询到的结果。


```
impl Solution {
    /// 处理查询，返回每个查询值在数组中的原始位置，并将该值移动到数组开头
    /// 算法：维护当前排列 `v`（初始为 1..=m），对于每个查询 `q`：
    ///       找到 `q` 在 `v` 中的索引 `p`，记录 `p`，然后将 `q` 移动到 `v` 开头
    pub fn process_queries(queries: Vec<i32>, m: i32) -> Vec<i32> {
        let initial: Vec<i32> = (1..=m).collect();
        let len = queries.len(); // 预先保存长度，避免移动后无法访问

        // 使用 fold 迭代处理每个查询，累加器为 (当前排列, 结果集)
        let (_, result) = queries.into_iter().fold(
            (initial, Vec::with_capacity(len)),
            |(mut v, mut ans), q| {
                // 查找 q 在 v 中的位置（0‑based）
                let pos = v.iter().position(|&x| x == q).expect("q 一定存在于 v 中");
                // 将 q 移动到开头
                v.remove(pos);
                v.insert(0, q);
                // 记录原始位置
                ans.push(pos as i32);
                (v, ans) // 返回新的累加器
            },
        );
        result
    }
}
```
