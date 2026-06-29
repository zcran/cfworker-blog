---
title: "leetcode-分治35"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 查询树上回文路径

给你一棵包含 n 个节点的无向树，节点编号从 0 到 n - 1。树由一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi] 表示节点 ui 和 vi 之间存在一条无向边。

另给你一个长度为 n 且由小写英文字母组成的字符串 s，其中 s[i] 表示分配给节点 i 的字符。

还给你一个字符串数组 queries，其中每个 queries[i] 为以下形式之一：

· "update ui c"：将节点 ui 处的字符更改为 c。正式地，更新 s[ui] = c。
· "query ui vi"：判断从 ui 到 vi 的 唯一 路径（含两端点）上的字符所组成的字符串，是否可以 重新排列 成一个 回文串 。

返回一个布尔数组 answer，如果第 i 个类型为 "query ui vi" 的查询可以重新排列成 回文串 ，则 answer[i] 为 true，否则为 false。

回文串 是指正读和反读都相同的字符串。

```
use std::collections::HashMap;

/// 树状数组（Fenwick Tree）实现异或操作
struct FenwickTree {
    tree: Vec<i32>,
}

impl FenwickTree {
    /// 创建大小为 n 的树状数组（1-based 索引）
    fn new(n: usize) -> Self {
        Self {
            tree: vec![0; n + 1],
        }
    }

    /// 单点更新：在位置 i 异或 val
    /// 1 <= i <= n
    /// 时间复杂度 O(log n)
    fn update(&mut self, mut i: usize, val: i32) {
        while i < self.tree.len() {
            self.tree[i] ^= val;
            i += i & i.wrapping_neg(); // lowbit
        }
    }

    /// 前缀异或和：a[1] ^ a[2] ^ ... ^ a[i]
    /// 1 <= i <= n
    /// 时间复杂度 O(log n)
    fn prefix_xor(&self, mut i: usize) -> i32 {
        let mut res = 0;
        while i > 0 {
            res ^= self.tree[i];
            i -= i & i.wrapping_neg(); // lowbit
        }
        res
    }
}

/// LCA 倍增法结构体
struct LcaBinaryLifting {
    depth: Vec<usize>,
    parent: Vec<Vec<Option<usize>>>,
    tin: Vec<usize>,      // DFS 进入时间戳（1-based）
    tout: Vec<usize>,     // DFS 离开时间戳
    path_xor: Vec<i32>,   // 从根到节点的路径字母奇偶性
}

impl LcaBinaryLifting {
    /// 构建 LCA 数据结构
    fn new(edges: &[Vec<i32>], s: &str) -> Self {
        let n = edges.len() + 1;
        let log_level = (32 - (n as u32).leading_zeros()) as usize; // ceil(log2(n))

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // 初始化数据结构
        let mut depth = vec![0; n];
        let mut parent = vec![vec![None; n]; log_level];
        let mut tin = vec![0; n];
        let mut tout = vec![0; n];
        let mut path_xor = vec![0; n];

        // 根节点路径异或值
        let first_bit = 1 << (s.chars().next().unwrap() as usize - 'a' as usize);
        path_xor[0] = first_bit;

        // DFS 遍历
        let mut clock = 0;
        Self::dfs(
            0,
            n,
            &graph,
            &s,
            &mut depth,
            &mut parent,
            &mut tin,
            &mut tout,
            &mut path_xor,
            &mut clock,
        );

        // 倍增法构建祖先表
        for level in 0..log_level - 1 {
            for node in 0..n {
                if let Some(ancestor) = parent[level][node] {
                    parent[level + 1][node] = parent[level][ancestor];
                }
            }
        }

        Self {
            depth,
            parent,
            tin,
            tout,
            path_xor,
        }
    }

    /// DFS 递归实现
    fn dfs(
        node: usize,
        n: usize,
        graph: &[Vec<usize>],
        s: &str,
        depth: &mut [usize],
        parent: &mut [Vec<Option<usize>>],
        tin: &mut [usize],
        tout: &mut [usize],
        path_xor: &mut [i32],
        clock: &mut usize,
    ) {
        *clock += 1;
        tin[node] = *clock;

        for &child in &graph[node] {
            if Some(child) != parent[0][node] {
                depth[child] = depth[node] + 1;
                parent[0][child] = Some(node);

                // 计算路径异或值
                let char_bit = 1 << (s.chars().nth(child).unwrap() as usize - 'a' as usize);
                path_xor[child] = path_xor[node] ^ char_bit;

                Self::dfs(
                    child,
                    n,
                    graph,
                    s,
                    depth,
                    parent,
                    tin,
                    tout,
                    path_xor,
                    clock,
                );
            }
        }

        tout[node] = *clock;
    }

    /// 获取节点的第 k 级祖先
    fn get_kth_ancestor(&self, mut node: usize, mut k: usize) -> Option<usize> {
        while k > 0 && node < self.parent[0].len() {
            let step = k.trailing_zeros() as usize;
            if let Some(ancestor) = self.parent[step][node] {
                node = ancestor;
            } else {
                return None;
            }
            k &= k - 1; // 移除最低位
        }
        Some(node)
    }

    /// 获取两个节点的最近公共祖先（LCA）
    fn get_lca(&self, mut x: usize, mut y: usize) -> usize {
        // 确保 x 是较深的节点
        if self.depth[x] > self.depth[y] {
            std::mem::swap(&mut x, &mut y);
        }

        // 将 y 提升到与 x 同一深度
        let depth_diff = self.depth[y] - self.depth[x];
        y = self.get_kth_ancestor(y, depth_diff).unwrap_or(y);

        if y == x {
            return x;
        }

        // 二分查找 LCA
        for level in (0..self.parent.len()).rev() {
            if let (Some(px), Some(py)) = (self.parent[level][x], self.parent[level][y]) {
                if px != py {
                    x = px;
                    y = py;
                }
            }
        }

        self.parent[0][x].unwrap_or(0)
    }
}

impl Solution {
    /// 判断路径字符串是否可以重新排列成回文串
    ///
    /// # 算法原理
    /// 1. 使用树状数组维护子树更新（支持路径异或查询）
    /// 2. 路径异或值 = xor_from_root[x] ^ xor_from_root[y] ^ lca_char
    /// 3. 判断异或值中奇数个字符的数量 <= 1
    ///
    /// # 参数
    /// - `n`: 节点数量
    /// - `edges`: 边列表
    /// - `s`: 节点字符
    /// - `queries`: 查询列表，格式："update ui c" 或 "query ui vi"
    ///
    /// # 返回
    /// 每个 query 查询的结果
    ///
    /// # 复杂度
    /// - 预处理：O(n log n)
    /// - 每次查询：O(log n)
    /// - 总时间复杂度：O((n + q) log n)
    /// - 空间复杂度：O(n log n)
    pub fn palindrome_path(
        n: i32,
        edges: Vec<Vec<i32>>,
        s: String,
        queries: Vec<String>,
    ) -> Vec<bool> {
        let n = n as usize;
        let mut s_chars: Vec<char> = s.chars().collect();

        // 构建 LCA 数据结构
        let lca = LcaBinaryLifting::new(&edges, &s);

        // 初始化树状数组（用于子树异或更新）
        let mut bit = FenwickTree::new(n);

        let mut ans = Vec::new();

        for query in &queries {
            let parts: Vec<&str> = query.split_whitespace().collect();
            let op = parts[0];

            if op == "update" {
                let u: usize = parts[1].parse().unwrap();
                let c: char = parts[2].chars().next().unwrap();

                // 计算需要异或的值：擦除旧字符，添加新字符
                let old_bit = 1 << (s_chars[u] as usize - 'a' as usize);
                let new_bit = 1 << (c as usize - 'a' as usize);
                let val = old_bit ^ new_bit;

                // 更新字符
                s_chars[u] = c;

                // 子树 [tin[u], tout[u]] 全部异或 val
                bit.update(lca.tin[u], val);
                bit.update(lca.tout[u] + 1, val);
            } else {
                // query 操作
                let u: usize = parts[1].parse().unwrap();
                let v: usize = parts[2].parse().unwrap();

                let lca_node = lca.get_lca(u, v);
                let lca_char_bit = 1 << (s_chars[lca_node] as usize - 'a' as usize);

                // 计算路径异或值
                let path_xor = lca.path_xor[u]
                    ^ lca.path_xor[v]
                    ^ bit.prefix_xor(lca.tin[u])
                    ^ bit.prefix_xor(lca.tin[v])
                    ^ lca_char_bit;

                // 判断异或值中 1 的位数是否 <= 1
                let is_palindrome = (path_xor & (path_xor - 1)) == 0;
                ans.push(is_palindrome);
            }
        }

        ans
    }
}
```
