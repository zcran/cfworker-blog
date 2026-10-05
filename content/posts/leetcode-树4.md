---
title: "leetcode-树4"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 树中可以形成回文的路径数

给你一棵 树（即，一个连通、无向且无环的图），根 节点为 0 ，由编号从 0 到 n - 1 的 n 个节点组成。这棵树用一个长度为 n 、下标从 0 开始的数组 parent 表示，其中 parent[i] 为节点 i 的父节点，由于节点 0 为根节点，所以 parent[0] == -1 。

另给你一个长度为 n 的字符串 s ，其中 s[i] 是分配给 i 和 parent[i] 之间的边的字符。s[0] 可以忽略。

找出满足 u < v ，且从 u 到 v 的路径上分配的字符可以 重新排列 形成 回文 的所有节点对 (u, v) ，并返回节点对的数目。

如果一个字符串正着读和反着读都相同，那么这个字符串就是一个 回文 。


```
use std::collections::HashMap;

impl Solution {
    /// 计算所有满足 u < v 且路径字符可重排成回文的节点对数量。
    ///
    /// 核心思路：
    /// 1. 以 0 为根，计算从根到每个节点的路径异或 mask（26 位，1 表示对应字符出现奇数次）。
    /// 2. 对于任意两个节点 u, v，路径 (u, v) 的字符异或 = mask[u] ^ mask[v]。
    /// 3. 路径可重排成回文 ⇔ 异或结果的 popcount ≤ 1。
    /// 4. 深度优先遍历树，用 HashMap 维护已访问节点的 mask 出现次数。
    ///    每访问一个新节点，先统计它与之前所有节点的配对，再将自身 mask 加入。
    ///    由于 DFS 顺序，每个节点对只会在后访问的节点处被统计一次，无需回溯。
    pub fn count_palindrome_paths(parent: Vec<i32>, s: String) -> i64 {
        let n = parent.len();
        // 直接使用字节切片，s[i] 对应边 (parent[i], i) 的字符
        let s = s.as_bytes();

        // 建树：graph[u] 存储 u 的所有子节点
        let mut graph = vec![Vec::new(); n];
        for i in 1..n {
            graph[parent[i] as usize].push(i);
        }

        // mask -> 出现次数
        let mut cnt: HashMap<i32, i64> = HashMap::new();
        let mut ans = 0i64;

        // 递归 DFS
        fn dfs(
            u: usize,
            mask: i32,
            graph: &[Vec<usize>],
            s: &[u8],
            cnt: &mut HashMap<i32, i64>,
            ans: &mut i64,
        ) {
            // 1. 统计当前节点与之前节点的配对
            // 情况一：路径异或后恰好有一个字符出现奇数次（翻转任意一位）
            for i in 0..26 {
                if let Some(&v) = cnt.get(&(mask ^ (1 << i))) {
                    *ans += v;
                }
            }
            // 情况二：路径异或后所有字符都出现偶数次（异或结果为 0）
            if let Some(&v) = cnt.get(&mask) {
                *ans += v;
            }

            // 2. 将当前节点的 mask 加入计数（使用块作用域确保借用及时释放）
            {
                *cnt.entry(mask).or_insert(0) += 1;
            }

            // 3. 递归处理子节点
            for &v in &graph[u] {
                // s[v] 是边 (u, v) 上的字符，转换为 0..25 的位偏移
                let bit = 1 << (s[v] - b'a');
                dfs(v, mask ^ bit, graph, s, cnt, ans);
            }
        }

        dfs(0, 0, &graph, s, &mut cnt, &mut ans);
        ans
    }
}
```
