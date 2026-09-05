---
title: "leetcode-前缀和66"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 和有限的最长子序列

给你一个长度为 n 的整数数组 nums ，和一个长度为 m 的整数数组 queries 。

返回一个长度为 m 的数组 answer ，其中 answer[i] 是 nums 中 元素之和小于等于 queries[i] 的 子序列 的 最大 长度  。

子序列 是由一个数组删除某些元素（也可以不删除）但不改变剩余元素顺序得到的一个数组。


```
impl Solution {
    pub fn answer_queries(mut nums: Vec<i32>, queries: Vec<i32>) -> Vec<i32> {
        // 1. 排序原数组
        nums.sort_unstable();

        // 2. 计算前缀和：prefix[i] 表示前 i+1 个最小元素的和
        // 这样前缀和数组是严格递增的（因为 nums 都是正数）
        for i in 1..nums.len() {
            nums[i] += nums[i - 1];
        }

        // 3. 对每个查询，找到最大的前缀和 <= query 的位置
        queries
            .into_iter()
            .map(|query| {
                // 在有序前缀和数组中二分查找第一个 > query 的位置
                // 返回的索引即为最大长度
                let pos = nums.partition_point(|&x| x <= query);
                pos as i32
            })
            .collect()
    }
}
```
