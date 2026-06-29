---
title: "leetcode-并查集6"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 冗余连接 II

在本问题中，有根树指满足以下条件的 有向 图。该树只有一个根节点，所有其他节点都是该根节点的后继。该树除了根节点之外的每一个节点都有且只有一个父节点，而根节点没有父节点。

输入一个有向图，该图由一个有着 n 个节点（节点值不重复，从 1 到 n）的树及一条附加的有向边构成。附加的边包含在 1 到 n 中的两个不同顶点间，这条附加的边不属于树中已存在的边。

结果图是一个以边组成的二维数组 edges 。 每个元素是一对 [ui, vi]，用以表示 有向 图中连接顶点 ui 和顶点 vi 的边，其中 ui 是 vi 的一个父节点。

返回一条能删除的边，使得剩下的图是有 n 个节点的有根树。若有多个答案，返回最后出现在给定二维数组的答案。

```
impl Solution {
    pub fn find_redundant_directed_connection(mut edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = edges.len();
        let mut parent = vec![0; n + 1]; // parent[v] = u 记录节点 v 的父节点
        let mut candidate1 = vec![]; // 第一条冲突边（指向已有父节点的边）
        let mut candidate2 = vec![]; // 第二条冲突边（当前边）

        // 第一遍遍历：检测是否有节点有两个父节点
        for edge in &mut edges {
            let (u, v) = (edge[0], edge[1]);
            if parent[v as usize] != 0 {
                // 节点 v 已有父节点，产生冲突
                candidate1 = vec![parent[v as usize], v];
                candidate2 = vec![u, v];
                // 标记当前边为无效，后续并查集跳过它
                edge[0] = -1;
                edge[1] = -1;
            } else {
                parent[v as usize] = u;
            }
        }

        // 并查集：检测是否有环
        let mut uf_parent = (0..=n as i32).collect::<Vec<_>>();

        fn find(p: &mut [i32], mut x: i32) -> i32 {
            while p[x as usize] != x {
                p[x as usize] = p[p[x as usize] as usize];
                x = p[x as usize];
            }
            x
        }

        for edge in edges {
            let (u, v) = (edge[0], edge[1]);
            if u == -1 { continue; } // 跳过被标记的候选边

            let ru = find(&mut uf_parent, u);
            let rv = find(&mut uf_parent, v);
            if ru == rv {
                // 发现环，返回 candidate1 如果有冲突，否则返回当前边
                return if !candidate1.is_empty() { candidate1 } else { edge };
            }
            uf_parent[rv as usize] = ru;
        }

        // 无环但有冲突，删除第二条冲突边
        candidate2
    }
}
```
