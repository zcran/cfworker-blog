---
title: "leetcode-最短路19"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 转换字符串的最小成本 II

给你两个下标从 0 开始的字符串 source 和 target ，它们的长度均为 n 并且由 小写 英文字母组成。

另给你两个下标从 0 开始的字符串数组 original 和 changed ，以及一个整数数组 cost ，其中 cost[i] 代表将字符串 original[i] 更改为字符串 changed[i] 的成本。

你从字符串 source 开始。在一次操作中，如果 存在 任意 下标 j 满足 cost[j] == z  、original[j] == x 以及 changed[j] == y ，你就可以选择字符串中的 子串 x 并以 z 的成本将其更改为 y 。 你可以执行 任意数量 的操作，但是任两次操作必须满足 以下两个 条件 之一 ：

在两次操作中选择的子串分别是 source[a..b] 和 source[c..d] ，满足 b < c  或 d < a 。换句话说，两次操作中选择的下标 不相交 。
在两次操作中选择的子串分别是 source[a..b] 和 source[c..d] ，满足 a == c 且 b == d 。换句话说，两次操作中选择的下标 相同 。
返回将字符串 source 转换为字符串 target 所需的 最小 成本。如果不可能完成转换，则返回 -1 。

注意，可能存在下标 i 、j 使得 original[j] == original[i] 且 changed[j] == changed[i] 。

```
use std::collections::HashMap;

const INF: i32 = i32::MAX / 2;

/// Trie 节点，用于存储字符串并分配唯一 ID。
struct TrieNode {
    children: [Option<Box<TrieNode>>; 26], // 26 个子节点
    id: i32,                               // 当前节点代表的字符串的 ID
}

impl TrieNode {
    /// 创建一个新的 Trie 节点。
    fn new() -> Self {
        Self {
            children: Default::default(),
            id: -1,
        }
    }

    /// 向 Trie 中插入一个单词，返回其 ID。
    /// 如果单词已存在，直接返回已有 ID；否则分配新 ID。
    fn add(&mut self, word: &str, next_id: &mut i32) -> i32 {
        let mut node = self;
        for ch in word.chars() {
            let idx = (ch as u8 - b'a') as usize;
            node = node.children[idx]
                .get_or_insert_with(|| Box::new(TrieNode::new()));
        }
        if node.id == -1 {
            *next_id += 1;
            node.id = *next_id;
        }
        node.id
    }
}

impl Solution {
    /// 计算将字符串 source 转换为 target 的最小总代价。
    /// 允许将任意子串（对应 original 中的某个词）替换为 changed 中的对应词，
    /// 每个替换操作有给定 cost。同时允许单个字符的替换（可视为长度为 1 的子串）。
    /// 如果无法转换，返回 -1。
    pub fn minimum_cost(
        source: String,
        target: String,
        original: Vec<String>,
        changed: Vec<String>,
        cost: Vec<i32>,
    ) -> i64 {
        let n = source.len();
        let m = original.len();

        // 构建 Trie 并为每个出现的字符串分配唯一 ID
        let mut root = TrieNode::new();
        let mut next_id = -1; // 当前最大 ID

        // 将 original 和 changed 中的所有字符串插入 Trie，记录对应的 ID
        let edges: Vec<(i32, i32, i32)> = (0..m)
            .map(|i| {
                let from_id = TrieNode::add(&mut root, &original[i], &mut next_id);
                let to_id = TrieNode::add(&mut root, &changed[i], &mut next_id);
                (from_id, to_id, cost[i])
            })
            .collect();

        let node_count = (next_id + 1) as usize; // 实际节点数

        // 初始化距离矩阵，所有节点到自身的距离为 0，其余为 INF
        let mut dist = vec![vec![INF; node_count]; node_count];
        for i in 0..node_count {
            dist[i][i] = 0;
        }

        // 根据直接转换边更新距离矩阵
        for (u, v, c) in edges {
            let u = u as usize;
            let v = v as usize;
            if c < dist[u][v] {
                dist[u][v] = c;
            }
        }

        // Floyd-Warshall 求所有节点对之间的最短转换代价
        for k in 0..node_count {
            for i in 0..node_count {
                if dist[i][k] == INF {
                    continue;
                }
                for j in 0..node_count {
                    let via = dist[i][k] + dist[k][j];
                    if via < dist[i][j] {
                        dist[i][j] = via;
                    }
                }
            }
        }

        // DP 数组：f[i] 表示将 source[0..=i] 转换为 target[0..=i] 的最小代价
        let mut dp = vec![-1i64; n];
        let source_chars: Vec<char> = source.chars().collect();
        let target_chars: Vec<char> = target.chars().collect();

        for j in 0..n {
            // 如果前一个位置不可达，则当前也无法从那里转移
            if j > 0 && dp[j - 1] == -1 {
                continue;
            }
            let base = if j == 0 { 0 } else { dp[j - 1] };

            // 情形1：直接匹配当前字符（无需替换）
            if source_chars[j] == target_chars[j] {
                if dp[j] == -1 || base < dp[j] {
                    dp[j] = base;
                }
            }

            // 情形2：尝试使用任意长度的子串替换
            let mut u_node = &root;
            let mut v_node = &root;
            for i in j..n {
                let u_idx = (source_chars[i] as u8 - b'a') as usize;
                let v_idx = (target_chars[i] as u8 - b'a') as usize;

                // 沿着 Trie 向下走，若某字符不存在则无法继续扩展
                if let Some(child) = u_node.children[u_idx].as_ref() {
                    u_node = child;
                } else {
                    break;
                }
                if let Some(child) = v_node.children[v_idx].as_ref() {
                    v_node = child;
                } else {
                    break;
                }

                // 若当前前缀恰好是 Trie 中的某个完整字符串，且存在转换路径
                if u_node.id != -1 && v_node.id != -1 {
                    let u_id = u_node.id as usize;
                    let v_id = v_node.id as usize;
                    if dist[u_id][v_id] != INF {
                        let new_cost = base + dist[u_id][v_id] as i64;
                        if dp[i] == -1 || new_cost < dp[i] {
                            dp[i] = new_cost;
                        }
                    }
                }
            }
        }

        dp[n - 1] // 最终结果，若不可达则为 -1
    }
}
```
