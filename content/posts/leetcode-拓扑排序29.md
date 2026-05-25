---
title: "leetcode-拓扑排序29"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 序列重建

给定一个长度为 n 的整数数组 nums ，其中 nums 是范围为 [1，n] 的整数的排列。还提供了一个 2D 整数数组 sequences ，其中 sequences[i] 是 nums 的子序列。
检查 nums 是否是唯一的最短 超序列 。最短 超序列 是 长度最短 的序列，并且所有序列 sequences[i] 都是它的子序列。对于给定的数组 sequences ，可能存在多个有效的 超序列 。

例如，对于 sequences = [[1,2],[1,3]] ，有两个最短的 超序列 ，[1,2,3] 和 [1,3,2] 。
而对于 sequences = [[1,2],[1,3],[1,2,3]] ，唯一可能的最短 超序列 是 [1,2,3] 。[1,2,3,4] 是可能的超序列，但不是最短的。
如果 nums 是序列的唯一最短 超序列 ，则返回 true ，否则返回 false 。
子序列 是一个可以通过从另一个序列中删除一些元素或不删除任何元素，而不改变其余元素的顺序的序列。

```
impl Solution {
    /// 判断 `nums` 是否是由 `sequences` 唯一确定的拓扑序列。
    /// 算法：收集所有相邻数字对（u→v）作为有向边，然后检查 `nums` 中的每一对相邻元素是否都存在这条边。
    /// 若所有相邻对都满足，则 `nums` 是一个合法的重建序列。
    pub fn sequence_reconstruction(nums: Vec<i32>, sequences: Vec<Vec<i32>>) -> bool {
        // 构建边的集合：map[u] 存储所有 u 的后继节点 v
        let mut edges: std::collections::HashMap<i32, std::collections::HashSet<i32>> =
            std::collections::HashMap::new();

        // 遍历每一个给定的子序列
        for seq in sequences {
            // 使用 windows(2) 获取所有相邻元素对
            for pair in seq.windows(2) {
                let u = pair[0];
                let v = pair[1];
                // 插入边 u -> v
                edges.entry(u)
                     .or_insert_with(std::collections::HashSet::new)
                     .insert(v);
            }
        }

        // 检查 nums 中的每一对相邻元素 (a, b)
        nums.windows(2)
            .all(|pair| {
                let a = pair[0];
                let b = pair[1];
                // 若存在边 a -> b 则返回 true，否则 false
                edges.get(&a)
                     .map_or(false, |succ| succ.contains(&b))
            })
    }
}
```
