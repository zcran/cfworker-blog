---
title: "leetcode-拓扑排序16"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 相邻字符不同的最长路径

给你一棵 树（即一个连通、无向、无环图），根节点是节点 0 ，这棵树由编号从 0 到 n - 1 的 n 个节点组成。用下标从 0 开始、长度为 n 的数组 parent 来表示这棵树，其中 parent[i] 是节点 i 的父节点，由于节点 0 是根节点，所以 parent[0] == -1 。

另给你一个字符串 s ，长度也是 n ，其中 s[i] 表示分配给节点 i 的字符。

请你找出路径上任意一对相邻节点都没有分配到相同字符的 最长路径 ，并返回该路径的长度。



```
impl Solution {
    /// 返回树中最长的路径长度，路径上相邻节点的字符必须不同。
    /// 树由父节点数组 `parent` 给出，根节点为 0，`parent[0] = -1`。
    /// 每个节点的字符由字符串 `s` 给出。
    pub fn longest_path(parent: Vec<i32>, s: String) -> i32 {
        let n = parent.len();
        let s_bytes = s.as_bytes();               // 转为字节数组，便于 O(1) 比较

        // 构建孩子列表（邻接表）
        let mut children = vec![Vec::new(); n];
        for (i, &p) in parent.iter().enumerate().skip(1) {
            children[p as usize].push(i);
        }

        let mut global_max = 1;                  // 至少存在一个节点，路径长度为 1

        /// 深度优先搜索，返回以 `node` 为端点的最长链长度，
        /// 同时通过可变引用 `global_max` 更新全局最长路径。
        fn dfs(node: usize, children: &[Vec<usize>], s_bytes: &[u8], global_max: &mut i32) -> i32 {
            // longest_from_children 存储从不同孩子延伸过来的最长链长度
            // 但我们只需要最大的两个（因为路径由两个不同子树的链拼接而成）
            let mut max1 = 0;    // 当前最大链长
            let mut max2 = 0;    // 当前第二大的链长

            for &child in &children[node] {
                let child_len = dfs(child, children, s_bytes, global_max);
                if s_bytes[child] != s_bytes[node] {
                    // 只有字符不同时，才能连接
                    if child_len > max1 {
                        max2 = max1;
                        max1 = child_len;
                    } else if child_len > max2 {
                        max2 = child_len;
                    }
                }
            }

            // 更新全局答案：当前节点作为路径最高点时，路径长度 = max1 + max2 + 1
            *global_max = (*global_max).max(max1 + max2 + 1);

            // 返回以当前节点为端点的最长链长度（即最大子链 + 自身）
            max1 + 1
        }

        dfs(0, &children, s_bytes, &mut global_max);
        global_max
    }
}
```
