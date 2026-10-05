---
title: "leetcode-树12"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 修改后子树的大小

给你一棵 n 个节点且根节点为编号 0 的树，节点编号为 0 到 n - 1 。这棵树用一个长度为 n 的数组 parent 表示，其中 parent[i] 是第 i 个节点的父亲节点的编号。由于节点 0 是根，parent[0] == -1 。

给你一个长度为 n 的字符串 s ，其中 s[i] 是节点 i 对应的字符。

对于节点编号从 1 到 n - 1 的每个节点 x ，我们 同时 执行以下操作 一次 ：

找到距离节点 x 最近 的祖先节点 y ，且 s[x] == s[y] 。
如果节点 y 不存在，那么不做任何修改。
否则，将节点 x 与它父亲节点之间的边 删除 ，在 x 与 y 之间连接一条边，使 y 变为 x 新的父节点。

请你返回一个长度为 n 的数组 answer ，其中 answer[i] 是 最终 树中，节点 i 为根的 子树 的 大小 。


```
impl Solution {
    /// 题意：
    /// 对于每个节点 x (1..n-1)，在原始树中找到距离 x 最近的祖先 y 使得 s[x] == s[y]。
    /// 若存在，则将 x 的父节点改为 y；否则保持不变。所有操作同时基于原始树进行。
    /// 最后返回最终树中每个节点为根的子树大小。
    ///
    /// 思路：
    /// 1. 一次 DFS 计算每个节点的最终父节点 final_parent：
    ///    - 维护 ancestor[26]，记录当前路径上每种字符最近出现的节点。
    ///    - 进入节点 u 时，字符 c = s[u]，若 ancestor[c] != -1，则 final_parent[u] = ancestor[c]；
    ///      否则 final_parent[u] = parent[u]（原始父节点）。
    ///    - 然后更新 ancestor[c] = u，递归子节点，回溯时恢复 ancestor[c]。
    /// 2. 根据 final_parent 构建最终树。
    /// 3. 对最终树做一次后序遍历，计算每个节点的子树大小。
    pub fn find_subtree_sizes(parent: Vec<i32>, s: String) -> Vec<i32> {
        let n = parent.len();
        let s = s.as_bytes();

        // 原始树的子节点列表
        let mut children = vec![Vec::new(); n];
        for i in 1..n {
            let p = parent[i] as usize;
            children[p].push(i);
        }

        // 计算每个节点的最终父节点
        let mut final_parent = vec![-1i32; n];
        let mut ancestor = [-1i32; 26];

        // 迭代 DFS，避免递归深度过大
        struct Frame {
            u: usize,
            idx: usize,
            old: i32,
        }
        let mut stack = Vec::with_capacity(n);

        // 根节点入栈
        let c0 = (s[0] - b'a') as usize;
        let old0 = ancestor[c0];
        ancestor[c0] = 0;
        stack.push(Frame { u: 0, idx: 0, old: old0 });

        while let Some(frame) = stack.last_mut() {
            let u = frame.u;
            if frame.idx < children[u].len() {
                let v = children[u][frame.idx];
                frame.idx += 1;

                let cv = (s[v] - b'a') as usize;
                let old_v = ancestor[cv];
                // 确定 v 的最终父节点
                final_parent[v] = if old_v != -1 { old_v } else { parent[v] };

                ancestor[cv] = v as i32;
                stack.push(Frame { u: v, idx: 0, old: old_v });
            } else {
                // 回溯，恢复 ancestor
                let c = (s[u] - b'a') as usize;
                ancestor[c] = frame.old;
                stack.pop();
            }
        }

        // 构建最终树
        let mut final_children = vec![Vec::new(); n];
        for i in 1..n {
            let p = final_parent[i];
            // final_parent[i] 一定 >= 0（因为 parent[i] >= 0）
            final_children[p as usize].push(i);
        }

        // 后序遍历计算子树大小
        let mut subtree_size = vec![1i32; n];
        let mut order = Vec::with_capacity(n);
        let mut visited = vec![false; n];
        let mut st = vec![0usize];
        visited[0] = true;

        while let Some(u) = st.pop() {
            order.push(u);
            for &v in &final_children[u] {
                if !visited[v] {
                    visited[v] = true;
                    st.push(v);
                }
            }
        }

        // 逆序（自底向上）累加子树大小
        for &u in order.iter().rev() {
            for &v in &final_children[u] {
                subtree_size[u] += subtree_size[v];
            }
        }

        subtree_size
    }
}
```
