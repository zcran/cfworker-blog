---
title: "leetcode-哈希表44"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 从相邻元素对还原数组

存在一个由 n 个不同元素组成的整数数组 nums ，但你已经记不清具体内容。好在你还记得 nums 中的每一对相邻元素。

给你一个二维整数数组 adjacentPairs ，大小为 n - 1 ，其中每个 adjacentPairs[i] = [ui, vi] 表示元素 ui 和 vi 在 nums 中相邻。

题目数据保证所有由元素 nums[i] 和 nums[i+1] 组成的相邻元素对都存在于 adjacentPairs 中，存在形式可能是 [nums[i], nums[i+1]] ，也可能是 [nums[i+1], nums[i]] 。这些相邻元素对可以 按任意顺序 出现。

返回 原始数组 nums 。如果存在多种解答，返回 其中任意一个 即可。


```
use std::collections::HashMap;

impl Solution {
    pub fn restore_array(adjacent_pairs: Vec<Vec<i32>>) -> Vec<i32> {
        // 邻接表：每个值对应它在数组中相邻的值（度数为 1 的是端点，2 的是中间点）。
        let mut adj: HashMap<i32, Vec<i32>> = HashMap::new();
        for pair in &adjacent_pairs {
            adj.entry(pair[0]).or_default().push(pair[1]);
            adj.entry(pair[1]).or_default().push(pair[0]);
        }

        let n = adjacent_pairs.len() + 1;
        // 找端点（只有一个邻居）作为起点。
        let mut start = 0;
        for (&k, v) in &adj {
            if v.len() == 1 {
                start = k;
                break;
            }
        }

        // 依次递推：当前点的下一个点是其邻居中不等于前一个点的那个。
        let mut ans = Vec::with_capacity(n);
        ans.push(start);
        ans.push(adj[&start][0]);
        for i in 2..n {
            let prev = ans[i - 2];
            let cur = ans[i - 1];
            let neighbors = &adj[&cur];
            ans.push(if neighbors[0] == prev { neighbors[1] } else { neighbors[0] });
        }
        ans
    }
}
```
