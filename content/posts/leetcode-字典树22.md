---
title: "leetcode-字典树22"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 查询最大基因差

给你一棵 n 个节点的有根树，节点编号从 0 到 n - 1 。每个节点的编号表示这个节点的 独一无二的基因值 （也就是说节点 x 的基因值为 x）。两个基因值的 基因差 是两者的 异或和 。给你整数数组 parents ，其中 parents[i] 是节点 i 的父节点。如果节点 x 是树的 根 ，那么 parents[x] == -1 。

给你查询数组 queries ，其中 queries[i] = [nodei, vali] 。对于查询 i ，请你找到 vali 和 pi 的 最大基因差 ，其中 pi 是节点 nodei 到根之间的任意节点（包含 nodei 和根节点）。更正式的，你想要最大化 vali XOR pi 。

请你返回数组 ans ，其中 ans[i] 是第 i 个查询的答案。

```
impl Solution {
    pub fn max_genetic_difference(parents: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 获取树中节点的数量
        let n = parents.len();

        // ========== 1. 构建树的邻接表 ==========
        // children[i] 存储节点 i 的所有子节点
        let mut children = vec![vec![]; n];
        let mut root = 0;  // 根节点编号，初始化为0

        // 遍历所有节点，构建父子关系
        for (i, &p) in parents.iter().enumerate() {
            if p == -1 {
                // 父节点为 -1 表示当前节点是根节点
                root = i;
            } else {
                // 否则将当前节点添加到父节点的子节点列表中
                children[p as usize].push(i);
            }
        }

        // ========== 2. 离线处理查询 ==========
        // 离线：将查询按照节点分组，避免在DFS中重复查找
        let q = queries.len();  // 查询总数
        // node_queries[u] 存储所有针对节点 u 的查询
        // 每个查询是一个元组：(查询索引, 查询的val值)
        let mut node_queries = vec![vec![]; n];
        for (i, qry) in queries.iter().enumerate() {
            // qry[0] 是节点编号，qry[1] 是 val 值
            // 将查询索引 i 和 val 值绑定到对应节点
            node_queries[qry[0] as usize].push((i, qry[1]));
        }

        // ========== 3. 初始化 Trie（字典树）==========
        // 使用数组实现 Trie，每个节点有3个字段：[child0, child1, cnt]
        // - child0: 当前位为0的子节点索引（0表示不存在）
        // - child1: 当前位为1的子节点索引（0表示不存在）
        // - cnt: 经过该节点的数字数量（用于判断是否存在）
        let mut trie = vec![[0usize; 3]];  // 根节点索引为0，初始cnt=0
        trie.reserve(n * 18);  // 预分配内存：每个数字最多18位，避免频繁扩容

        // ========== 4. 结果数组 ==========
        let mut ans = vec![0; q];  // 存储每个查询的答案

        // ========== 5. DFS 递归函数 ==========
        // 使用闭包/函数递归遍历树，同时维护 Trie
        fn dfs(
            u: usize,                      // 当前节点编号
            children: &[Vec<usize>],       // 树的邻接表（只读）
            node_queries: &[Vec<(usize, i32)>], // 每个节点的查询列表（只读）
            trie: &mut Vec<[usize; 3]>,    // Trie（可修改）
            ans: &mut [i32],               // 答案数组（可修改）
        ) {
            // ---------- 5.1 插入当前节点到 Trie ----------
            let mut node = 0;  // 从根节点开始
            // 从高位到低位处理（题目说明最大18位，索引0-17）
            for shift in (0..=17).rev() {
                // 获取当前节点编号在当前位的二进制值（0或1）
                let bit = ((u as i32 >> shift) & 1) as usize;
                // 获取子节点索引
                let next = trie[node][bit];
                if next == 0 {
                    // 子节点不存在，创建新节点
                    let new = trie.len();      // 新节点的索引
                    trie.push([0; 3]);         // 添加新节点（所有字段初始化为0）
                    trie[node][bit] = new;     // 将当前节点的子指针指向新节点
                    node = new;                // 移动到新节点
                } else {
                    // 子节点存在，直接移动
                    node = next;
                }
                // 增加经过当前节点的数字计数
                // trie[node][2] 是 cnt 字段
                trie[node][2] += 1;
            }
            // 插入完成后，路径上每个节点的计数都增加了1

            // ---------- 5.2 处理当前节点的所有查询 ----------
            // 遍历绑定到当前节点 u 的所有查询
            for &(idx, val) in &node_queries[u] {
                let mut node = 0;  // 从根节点开始
                let mut res = 0;   // 存储最大异或结果
                // 从高位到低位贪心查找
                for shift in (0..=17).rev() {
                    // 获取 val 在当前位的值
                    let bit = ((val >> shift) & 1) as usize;
                    // 期望的位：与当前位相反（bit ^ 1 = 1-bit）
                    let prefer = bit ^ 1;
                    // 获取首选子节点（期望的位）
                    let prefer_child = trie[node][prefer];
                    // 检查首选子节点是否存在且包含至少一个数字
                    if prefer_child != 0 && trie[prefer_child][2] > 0 {
                        // 可以走首选路径，异或结果当前位为1
                        res |= 1 << shift;
                        node = prefer_child;  // 移动到首选子节点
                    } else {
                        // 首选路径不可用，只能走相同位的路径
                        node = trie[node][bit];
                        // 此时异或结果当前位为0，res保持不变
                    }
                }
                // 存储查询结果
                ans[idx] = res;
            }

            // ---------- 5.3 递归处理所有子节点 ----------
            // 深度优先遍历，确保回溯时正确维护 Trie
            for &v in &children[u] {
                dfs(v, children, node_queries, trie, ans);
            }

            // ---------- 5.4 回溯：从 Trie 中删除当前节点 ----------
            let mut node = 0;  // 从根节点开始
            for shift in (0..=17).rev() {
                // 获取当前节点编号在当前位的值
                let bit = ((u as i32 >> shift) & 1) as usize;
                // 移动到子节点（插入时一定存在）
                node = trie[node][bit];
                // 减少经过该节点的计数
                trie[node][2] -= 1;
            }
            // 删除完成后，路径上每个节点的计数都减少了1
            // 注意：我们不实际删除节点本身，只减少计数
            // 这样可以复用节点，避免频繁分配/释放内存
        }

        // ========== 6. 执行 DFS ==========
        // 从根节点开始深度优先遍历整棵树
        dfs(root, &children, &node_queries, &mut trie, &mut ans);

        // 返回所有查询的答案
        ans
    }
}
```
