---
title: "leetcode-二分查找52"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 每一个查询的最大美丽值

给你一个二维整数数组 items ，其中 items[i] = [pricei, beautyi] 分别表示每一个物品的 价格 和 美丽值 。

同时给你一个下标从 0 开始的整数数组 queries 。对于每个查询 queries[j] ，你想求出价格小于等于 queries[j] 的物品中，最大的美丽值 是多少。如果不存在符合条件的物品，那么查询的结果为 0 。

请你返回一个长度与 queries 相同的数组 answer，其中 answer[j]是第 j 个查询的答案。


```
impl Solution {
    pub fn maximum_beauty(items: Vec<Vec<i32>>, queries: Vec<i32>) -> Vec<i32> {
        // 按价格升序排序
        let mut items = items;
        items.sort_unstable_by_key(|it| it[0]);

        // 将美丽值改为前缀最大值（即价格 <= 当前价格时的最大美丽值）
        for i in 1..items.len() {
            items[i][1] = items[i][1].max(items[i - 1][1]);
        }

        // 对每个查询二分查找价格 <= q 的最后一个物品
        queries
            .into_iter()
            .map(|q| {
                let idx = items.partition_point(|it| it[0] <= q);
                if idx == 0 { 0 } else { items[idx - 1][1] }
            })
            .collect()
    }
}
```
