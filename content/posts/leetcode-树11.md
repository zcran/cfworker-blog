---
title: "leetcode-树11"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 判断 DFS 字符串是否是回文串

给你一棵 n 个节点的树，树的根节点为 0 ，n 个节点的编号为 0 到 n - 1 。这棵树用一个长度为 n 的数组 parent 表示，其中 parent[i] 是节点 i 的父节点。由于节点 0 是根节点，所以 parent[0] == -1 。

给你一个长度为 n 的字符串 s ，其中 s[i] 是节点 i 对应的字符。

一开始你有一个空字符串 dfsStr ，定义一个递归函数 dfs(int x) ，它的输入是节点 x ，并依次执行以下操作：

· 按照 节点编号升序 遍历 x 的所有孩子节点 y ，并调用 dfs(y) 。
· 将 字符 s[x] 添加到字符串 dfsStr 的末尾。

注意，所有递归函数 dfs 都共享全局变量 dfsStr 。

你需要求出一个长度为 n 的布尔数组 answer ，对于 0 到 n - 1 的每一个下标 i ，你需要执行以下操作：

· 清空字符串 dfsStr 并调用 dfs(i) 。
· 如果结果字符串 dfsStr 是一个 回文串 ，answer[i] 为 true ，否则 answer[i] 为 false 。

请你返回数组 answer 。


```
impl Solution {
    /// 题意：对每个节点 i，以 i 为根执行 dfs，得到的 dfsStr（后序：先子后自身）
    /// 判断是否为回文。等价于判断节点 i 的子树按“子节点编号升序”后序遍历得到的字符串是否回文。
    ///
    /// 关键观察：
    /// 1. 只需在原树（根为 0）上做一次后序遍历（子节点按编号升序），
    ///    得到全局 dfsStr 和一个时间区间 [in_time[i], out_time[i])，
    ///    该区间内的字符正是以 i 为根执行 dfs 得到的字符串（顺序一致）。
    /// 2. 于是问题转化为：对每个节点 i，判断其对应区间子串是否为回文。
    /// 3. 用 Manacher 预处理全局字符串的所有回文信息，O(1) 回答每个区间。
    ///
    /// 实现要点：
    /// - 后序遍历：先递归所有子节点，再把自身字符加入 dfsStr。
    /// - 子节点按编号升序：建图时自然按 i 从小到大 push，遍历顺序即为升序。
    pub fn find_answer(parent: Vec<i32>, s: String) -> Vec<bool> {
        let n = parent.len();
        let s = s.as_bytes();

        // 建树：graph[u] 存储 u 的所有子节点，按编号升序（i 从小到大）
        let mut graph = vec![Vec::new(); n];
        for i in 1..n {
            graph[parent[i] as usize].push(i);
        }

        // 后序遍历：记录每个节点的字符在 dfsStr 中的位置
        // post_order[i] = 节点 i 的字符在 dfsStr 中的下标
        let mut post_order = vec![0usize; n];
        let mut dfs_str = Vec::with_capacity(n);
        let mut clock = 0usize;

        // 迭代后序遍历，避免递归深度过大
        // 栈元素：(节点, 子节点处理进度)
        let mut stack = vec![(0usize, 0usize)];
        while let Some((u, idx)) = stack.pop() {
            if idx < graph[u].len() {
                // 还有子节点未处理：先处理当前子节点，再回到 u 处理下一个
                stack.push((u, idx + 1));
                let v = graph[u][idx];
                stack.push((v, 0));
            } else {
                // 所有子节点已处理，记录自身
                post_order[u] = clock;
                dfs_str.push(s[u]);
                clock += 1;
            }
        }

        // Manacher 预处理
        // 构造带分隔符的字符串：^ # c0 # c1 # ... # $
        let mut t = Vec::with_capacity(2 * n + 3);
        t.push(b'^');
        for &c in &dfs_str {
            t.push(b'#');
            t.push(c);
        }
        t.push(b'#');
        t.push(b'$');

        let m = t.len();
        let mut p = vec![0usize; m]; // p[i]：以 i 为中心的回文半径（含中心）
        let (mut center, mut right) = (0usize, 0usize);

        for i in 1..m - 1 {
            if i < right {
                p[i] = p[2 * center - i].min(right - i);
            } else {
                p[i] = 1;
            }
            while t[i + p[i]] == t[i - p[i]] {
                p[i] += 1;
            }
            if i + p[i] > right {
                center = i;
                right = i + p[i];
            }
        }

        // 计算每个节点的子树大小
        // 注意：后序中，子节点的 post_order 小于父节点，因此按 post_order 升序排列后，
        // 顺序遍历即为自底向上，可正确累加子树大小。
        let mut subtree_size = vec![1usize; n];
        let mut order: Vec<usize> = (0..n).collect();
        order.sort_unstable_by_key(|&i| post_order[i]);

        for &u in order.iter() {
            for &v in &graph[u] {
                subtree_size[u] += subtree_size[v];
            }
        }

        // 对每个节点，判断其子树对应的 dfsStr 区间是否为回文
        // 节点 i 的子树在 dfsStr 中的区间为 [L, R]：
        //   R = post_order[i]（i 最后被加入）
        //   L = R - subtree_size[i] + 1
        let mut ans = vec![false; n];
        for i in 0..n {
            let r = post_order[i];
            let l = r + 1 - subtree_size[i];
            // 映射到 t 中的位置：原下标 x -> 2*x + 2
            let tl = 2 * l + 2;
            let tr = 2 * r + 2;
            let mid = (tl + tr) / 2;
            // 需要回文半径覆盖整个区间
            ans[i] = p[mid] >= (tr - tl) / 2 + 1;
        }

        ans
    }
}
```
