---
title: "leetcode-拓扑排序28"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 火星词典

现有一种使用英语字母的外星文语言，这门语言的字母顺序与英语顺序不同。

给定一个字符串列表 words ，作为这门语言的词典，words 中的字符串已经 按这门新语言的字母顺序进行了排序 。

请你根据该词典还原出此语言中已知的字母顺序，并 按字母递增顺序 排列。若不存在合法字母顺序，返回 "" 。若存在多种可能的合法字母顺序，返回其中 任意一种 顺序即可。

字符串 s 字典顺序小于 字符串 t 有两种情况：

在第一个不同字母处，如果 s 中的字母在这门外星语言的字母顺序中位于 t 中字母之前，那么 s 的字典顺序小于 t 。
如果前面 min(s.length, t.length) 字母都相同，那么 s.length < t.length 时，s 的字典顺序也小于 t 。

```
impl Solution {
    /// 给定一个外星语词典中的单词列表（按字典序排序），返回字符的顺序字符串。
    /// 如果顺序不合法（存在环或前缀矛盾），返回空字符串。
    pub fn alien_order(words: Vec<String>) -> String {
        use std::collections::HashSet;

        // 节点状态
        #[derive(Clone, Copy, PartialEq, Eq)]
        enum State {
            Unused,   // 未在单词中出现
            Present,  // 出现过但尚未访问（拓扑排序未处理）
            Visiting, // 当前 DFS 栈中（用于检测环）
            Visited,  // 已处理完毕
        }
        use State::*;

        // 将字符转换为索引（0..25）
        fn to_idx(c: char) -> usize {
            (c as u8 - b'a') as usize
        }
        // 将索引转换回字符
        fn from_idx(i: usize) -> char {
            (i as u8 + b'a') as char
        }

        // 初始化：所有字符默认未使用
        let mut node_state = vec![Unused; 26];
        let mut adj = vec![HashSet::new(); 26];

        // 标记所有出现过的字符
        for word in &words {
            for c in word.chars() {
                node_state[to_idx(c)] = Present;
            }
        }

        // 建图：比较相邻单词，找到第一个不同的字符，添加一条有向边
        for pair in words.windows(2) {
            let (s, t) = (&pair[0], &pair[1]);
            let mut found_diff = false;
            for (a, b) in s.chars().zip(t.chars()) {
                if a != b {
                    found_diff = true;
                    let u = to_idx(a);
                    let v = to_idx(b);
                    adj[u].insert(v);
                    break;
                }
            }
            // 若前缀情况：s 是 t 的前缀且 s 更长，则非法
            if !found_diff && s.len() > t.len() {
                return String::new();
            }
        }

        // DFS 拓扑排序，同时检测环
        let mut result = Vec::with_capacity(26);
        fn dfs(
            u: usize,
            adj: &[HashSet<usize>],
            state: &mut [State],
            stack: &mut Vec<char>,
        ) -> bool {
            state[u] = Visiting;
            for &v in &adj[u] {
                match state[v] {
                    Present => {
                        if !dfs(v, adj, state, stack) {
                            return false;
                        }
                    }
                    Visiting => return false, // 发现环
                    Visited => (),
                    Unused => unreachable!(),
                }
            }
            state[u] = Visited;
            stack.push(from_idx(u));
            true
        }

        // 对所有存在且未访问的节点进行 DFS
        for u in 0..26 {
            if node_state[u] == Present {
                if !dfs(u, &adj, &mut node_state, &mut result) {
                    result.clear();
                    break;
                }
            }
        }

        // 当前 result 是逆序的（后序压入），需要反转得到正确的拓扑序
        result.into_iter().rev().collect()
    }
}
```
