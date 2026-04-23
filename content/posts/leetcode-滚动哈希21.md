---
title: "leetcode-滚动哈希21"
date: 2026-04-17T20:00:32+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 统计前后缀下标对 II

给你一个下标从 0 开始的字符串数组 words 。

定义一个 布尔 函数 isPrefixAndSuffix ，它接受两个字符串参数 str1 和 str2 ：

当 str1 同时是 str2 的前缀（prefix）和后缀（suffix）时，isPrefixAndSuffix(str1, str2) 返回 true，否则返回 false。
例如，isPrefixAndSuffix("aba", "ababa") 返回 true，因为 "aba" 既是 "ababa" 的前缀，也是 "ababa" 的后缀，但是 isPrefixAndSuffix("abc", "abcd") 返回 false。

以整数形式，返回满足 i < j 且 isPrefixAndSuffix(words[i], words[j]) 为 true 的下标对 (i, j) 的 数量 。


```
impl Solution {
    /// 统计满足条件的字符串对 (i, j) 的数量，其中 i < j 且 words[j] 同时以 words[i] 为前缀和后缀。
    /// 使用 Z 算法 + Trie 树，时间复杂度 O(总字符数)，空间复杂度 O(总字符数)。
    pub fn count_prefix_suffix_pairs(words: Vec<String>) -> i64 {
        // ---------- 1. Trie 节点定义 ----------
        // 使用索引池（Vec）存储节点，children 数组存储子节点索引（0 表示空）
        struct Node {
            children: [usize; 26],
            cnt: i64,   // 以此节点对应字符串为完整单词的个数
        }

        // 根节点索引为 0
        let mut nodes = vec![Node {
            children: [0; 26],
            cnt: 0,
        }];

        // ---------- 2. 辅助函数：计算 Z 数组 ----------
        // Z[i] = s 与 s[i..] 的最长公共前缀长度
        fn z_function(s: &str) -> Vec<usize> {
            let bytes = s.as_bytes();
            let n = bytes.len();
            let mut z = vec![0; n];
            if n == 0 {
                return z;
            }
            let mut l = 0;
            let mut r = 0;
            for i in 1..n {
                if i <= r {
                    z[i] = (z[i - l]).min(r - i + 1);
                }
                while i + z[i] < n && bytes[z[i]] == bytes[i + z[i]] {
                    l = i;
                    r = i + z[i];
                    z[i] += 1;
                }
            }
            z[0] = n;
            z
        }

        // ---------- 3. 处理单个字符串：在 Trie 中查找匹配并插入 ----------
        // 使用函数式风格：fold 遍历字符，同时维护 (当前节点索引, 累计匹配数)
        fn process_string(nodes: &mut Vec<Node>, s: &str) -> i64 {
            let bytes = s.as_bytes();
            let n = bytes.len();
            let z = z_function(s);   // 计算 Z 数组

            // 对字符串的每个字符索引进行 fold，初始状态 (当前节点, 匹配计数)
            let (_, match_cnt) = (0..n).fold((0, 0), |(cur, cnt), i| {
                let c = (bytes[i] - b'a') as usize;
                // 获取或创建子节点（这部分必须可变，因此 fold 内部无法避免突变）
                // 注意：fold 闭包中可以修改外部 nodes，但需要保证 nodes 是 &mut 且生命周期正确
                if nodes[cur].children[c] == 0 {
                    nodes.push(Node {
                        children: [0; 26],
                        cnt: 0,
                    });
                    nodes[cur].children[c] = nodes.len() - 1;
                }
                let next = nodes[cur].children[c];
                // 检查当前前缀是否也是后缀（Z 条件）
                let add = if z[n - 1 - i] == i + 1 { nodes[next].cnt } else { 0 };
                (next, cnt + add)
            });

            // 插入当前字符串：在最后一个字符对应的节点计数加 1
            // 注意：fold 已经返回了最后一个节点索引 `cur_final`，这里需要将其从结果中取出
            // 由于 fold 返回 (cur, cnt)，我们只需在 fold 后再增加计数
            // 为了保持函数式，可以在 fold 的同时累积插入，但那样会改变 fold 的语义。
            // 简单做法：fold 结束后单独更新计数。
            // 另一种方式：将插入也放入 fold，但会多一次循环。此处保持清晰。
            let final_node = (0..n).fold(0, |cur, i| {
                let c = (bytes[i] - b'a') as usize;
                if nodes[cur].children[c] == 0 {
                    nodes.push(Node {
                        children: [0; 26],
                        cnt: 0,
                    });
                    nodes[cur].children[c] = nodes.len() - 1;
                }
                nodes[cur].children[c]
            });
            nodes[final_node].cnt += 1;

            match_cnt
        }

        // ---------- 4. 主逻辑：遍历所有单词，累加结果 ----------
        words
            .iter()
            .fold((&mut nodes, 0), |(nodes, acc), word| {
                let cnt = process_string(nodes, word);
                (nodes, acc + cnt)
            })
            .1
    }
}
```
