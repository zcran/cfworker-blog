---
title: "leetcode-贪心42"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 用户分组

有 n 个人被分成数量未知的组。每个人都被标记为一个从 0 到 n - 1 的唯一ID 。

给定一个整数数组 groupSizes ，其中 groupSizes[i] 是第 i 个人所在的组的大小。例如，如果 groupSizes[1] = 3 ，则第 1 个人必须位于大小为 3 的组中。

返回一个组列表，使每个人 i 都在一个大小为 groupSizes[i] 的组中。

每个人应该 恰好只 出现在 一个组 中，并且每个人必须在一个组中。如果有多个答案，返回其中 任何 一个。可以 保证 给定输入 至少有一个 有效的解。


```
use std::collections::HashMap;

impl Solution {
    pub fn group_the_people(group_sizes: Vec<i32>) -> Vec<Vec<i32>> {
        let mut groups = Vec::new();
        // 按组大小分桶：key 为组大小，value 为当前还未成组的成员 ID
        let mut buckets: HashMap<i32, Vec<i32>> = HashMap::new();

        for (id, &size) in group_sizes.iter().enumerate() {
            let bucket = buckets.entry(size).or_default();
            bucket.push(id as i32);

            // 桶满即形成一个完整的组，移出并回收内存
            if bucket.len() == size as usize {
                groups.push(buckets.remove(&size).unwrap());
            }
        }

        groups
    }
}
```
