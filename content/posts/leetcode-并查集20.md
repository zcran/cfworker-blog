---
title: "leetcode-并查集20"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 等式方程的可满足性

给定一个由表示变量之间关系的字符串方程组成的数组，每个字符串方程 equations[i] 的长度为 4，并采用两种不同的形式之一："a==b" 或 "a!=b"。在这里，a 和 b 是小写字母（不一定不同），表示单字母变量名。

只有当可以将整数分配给变量名，以便满足所有给定的方程时才返回 true，否则返回 false。


```
impl Solution {
    /// 判断给定的等式/不等式方程组是否可满足。
    /// 使用并查集合并所有等式约束，然后检查所有不等式是否冲突。
    pub fn equations_possible(equations: Vec<String>) -> bool {
        // 26 个小写字母的并查集
        let mut parent = [0; 26];
        for i in 0..26 {
            parent[i] = i;
        }

        /// 查找根节点（带路径压缩）
        fn find(parent: &mut [usize; 26], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]]; // 隔代压缩，减少递归开销
                x = parent[x];
            }
            x
        }

        /// 合并两个集合
        fn union(parent: &mut [usize; 26], a: usize, b: usize) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra != rb {
                parent[ra] = rb; // 简单合并（节点数固定且少，无需按秩优化）
            }
        }

        // 字符转索引
        let idx = |c: u8| (c - b'a') as usize;

        // 1. 合并所有等式
        for eq in &equations {
            let b = eq.as_bytes();
            if b[1] == b'=' {
                union(&mut parent, idx(b[0]), idx(b[3]));
            }
        }

        // 2. 验证所有不等式
        for eq in &equations {
            let b = eq.as_bytes();
            let a = idx(b[0]);
            let c = idx(b[3]);
            if b[1] == b'!' && find(&mut parent, a) == find(&mut parent, c) {
                return false; // 不等式两端在同一集合 → 矛盾
            }
            // 等式已合并，无需再检查相等性（合并后必然相等）
        }

        true
    }
}
```
