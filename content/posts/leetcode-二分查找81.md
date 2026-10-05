---
title: "leetcode-二分查找81"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 距离最小相等元素查询

给你一个 环形 数组 nums 和一个数组 queries 。

对于每个查询 i ，你需要找到以下内容：

数组 nums 中下标 queries[i] 处的元素与 任意 其他下标 j（满足 nums[j] == nums[queries[i]]）之间的 最小 距离。如果不存在这样的下标 j，则该查询的结果为 -1 。

返回一个数组 answer，其大小与 queries 相同，其中 answer[i] 表示查询i的结果。


```
use std::collections::HashMap;

impl Solution {
    pub fn solve_queries(nums: Vec<i32>, queries: Vec<i32>) -> Vec<i32> {
        let n = nums.len() as i32;

        // 记录每个值出现的所有下标
        let mut positions: HashMap<i32, Vec<i32>> = HashMap::new();
        for (i, &v) in nums.iter().enumerate() {
            positions.entry(v).or_default().push(i as i32);
        }

        // 为每个值的下标列表首尾各加一个环绕点，方便处理环形距离
        for pos in positions.values_mut() {
            let first = pos[0];
            let last = *pos.last().unwrap();
            pos.insert(0, last - n);
            pos.push(first + n);
        }

        queries
            .iter()
            .map(|&q| {
                let pos = &positions[&nums[q as usize]];
                // 只有原数组中出现一次（加上环绕点共 3 个）时无解
                if pos.len() == 3 {
                    return -1;
                }
                // 找到第一个 >= q 的位置，比较它与左右相邻点的距离
                let i = pos.partition_point(|&v| v < q);
                (pos[i + 1] - pos[i]).min(pos[i] - pos[i - 1])
            })
            .collect()
    }
}
```
