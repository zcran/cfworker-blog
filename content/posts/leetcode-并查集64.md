---
title: "leetcode-并查集64"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 统计节点和为偶数的连通子图

给你一个无向图，有 n 个节点，编号从 0 到 n - 1。节点 i 的 值 为 nums[i]，可以是 0 或 1。图的边由一个二维数组 edges 给出，其中 edges[i] = [ui, vi] 表示节点 ui 和节点 vi 之间的一条边。

对于图中节点的 非空子集 s，我们考虑由 s 生成的 诱导子图 如下：

· 我们只保留 s 中的节点。
· 我们只保留两个端点都在 s 中的边。

返回一个整数，表示图中满足以下条件的节点的 非空 子集 s 的数量：

· s 的 诱导子图 是 连通的。
· s 中节点 值 的 总和 是 偶数。


```
impl Solution {
    pub fn even_sum_subgraphs(nums: Vec<i32>, edges: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();
        let mut mask = vec![0u32; n];
        for e in edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            mask[u] |= 1 << v;
            mask[v] |= 1 << u;
        }

        // 修复点：使用完全显式模式 &(_, val)
        let odd_mask = nums.iter()
            .enumerate()
            .filter(|&(_, val)| val % 2 == 1)
            .fold(0u32, |acc, (i, _)| acc | (1 << i));

        let full_mask = (1 << n) - 1;
        let mut ans = 0;

        for sub in 1..=full_mask {
            if ((sub & odd_mask).count_ones() & 1) != 0 {
                continue;
            }

            let start = sub & sub.wrapping_neg();
            let mut visited = sub ^ full_mask;
            let mut queue = start;
            visited |= start;

            while queue != 0 {
                let bit = queue & queue.wrapping_neg();
                queue ^= bit;
                let idx = bit.trailing_zeros() as usize;

                let neighbors = mask[idx] & !visited;
                queue |= neighbors;
                visited |= neighbors;
            }

            if visited == full_mask {
                ans += 1;
            }
        }

        ans
    }
}
```
