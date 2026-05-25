---
title: "leetcode-几何35"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 游乐园的游览计划

又到了一年一度的春游时间，小吴计划去游乐场游玩 1 天，游乐场总共有 N 个游乐项目，编号从 0 到 N-1。小吴给每个游乐项目定义了一个非负整数值 value[i] 表示自己的喜爱值。两个游乐项目之间会有双向路径相连，整个游乐场总共有 M 条双向路径，保存在二维数组 edges中。 小吴计划选择一个游乐项目 A 作为这一天游玩的重点项目。上午小吴准备游玩重点项目 A 以及与项目 A 相邻的两个项目 B、C （项目A、B与C要求是不同的项目，且项目B与项目C要求相邻），并返回 A ，即存在一条 A-B-C-A 的路径。 下午，小吴决定再游玩重点项目 A以及与A相邻的两个项目 B'、C'，（项目A、B'与C'要求是不同的项目，且项目B'与项目C'要求相邻），并返回 A ，即存在一条 A-B'-C'-A 的路径。下午游玩项目 B'、C' 可与上午游玩项目B、C存在重复项目。 小吴希望提前安排好游玩路径，使得喜爱值之和最大。请你返回满足游玩路径选取条件的最大喜爱值之和，如果没有这样的路径，返回 0。 注意：一天中重复游玩同一个项目并不能重复增加喜爱值了。例如：上下午游玩路径分别是 A-B-C-A与A-C-D-A 那么只能获得 value[A] + value[B] + value[C] + value[D] 的总和。



```
use std::collections::HashMap;

/// 表示一个三角形：三个顶点的权重和 + 三个顶点索引（已排序）
#[derive(Debug, Clone, Copy, PartialEq, Eq, Default)]
struct Triangle {
    sum: i32,
    nodes: [usize; 3],
}

impl Solution {
    pub fn max_weight(edges: Vec<Vec<i32>>, weight: Vec<i32>) -> i32 {
        let n = weight.len();               // 顶点数
        let m = edges.len();                // 边数

        // 邻接表：每个顶点的邻接顶点列表
        let mut adj = vec![vec![]; n];
        // 每个顶点关联的边索引列表（与 adj 顺序一致）
        let mut edge_of = vec![vec![]; n];
        // 边映射：(u, v) -> 边索引，其中 u < v
        let mut link = HashMap::new();

        for (id, e) in edges.iter().enumerate() {
            let u = e[0] as usize;
            let v = e[1] as usize;
            adj[u].push(v);
            adj[v].push(u);
            edge_of[u].push(id);
            edge_of[v].push(id);
            let (a, b) = if u < v { (u, v) } else { (v, u) };
            link.insert((a, b), id);
        }

        // 每个顶点关联的所有三角形
        let mut vertex_triangles = vec![vec![]; n];
        // 每条边关联的最大的3个三角形（按权重和降序）
        let mut edge_top3 = vec![[Triangle::default(); 3]; m];

        // 更新一条边的 top3 三角形
        let mut upload = |edge_id: usize, tri: Triangle| {
            let top3 = &mut edge_top3[edge_id];
            // 如果已经存在相同的三角形，忽略
            if top3.iter().any(|&t| t == tri) {
                return;
            }
            let mut arr = *top3;
            arr[2] = tri;
            arr.sort_by_key(|t| -t.sum);
            *top3 = arr;
        };

        // 记录一个三角形（由顶点 i,j,k 和边 a,b,c 构成）
        let mut update = |sum: i32, a: usize, b: usize, c: usize, i: usize, j: usize, k: usize| {
            let mut nodes = [i, j, k];
            nodes.sort_unstable();
            let tri = Triangle { sum, nodes };
            vertex_triangles[i].push(tri);
            vertex_triangles[j].push(tri);
            vertex_triangles[k].push(tri);
            upload(a, tri);
            upload(b, tri);
            upload(c, tri);
        };

        // 阈值 = sqrt(m)
        let deg = (m as f64).sqrt() as usize;
        let mut big_vertices = Vec::new();

        // 枚举所有三角形
        for v in 0..n {
            if adj[v].len() < deg {
                // 小度顶点：枚举其邻接点对
                let nb = &adj[v];
                for i in 0..nb.len() {
                    for j in i + 1..nb.len() {
                        let u = nb[i];
                        let w = nb[j];
                        let (a, b) = if u < w { (u, w) } else { (w, u) };
                        if let Some(&edge_id) = link.get(&(a, b)) {
                            let sum = weight[v] + weight[u] + weight[w];
                            let e1 = edge_of[v][i];
                            let e2 = edge_of[v][j];
                            update(sum, e1, e2, edge_id, v, u, w);
                        }
                    }
                }
            } else {
                big_vertices.push(v);
            }
        }

        // 大度顶点：枚举三元组
        for i in 0..big_vertices.len() {
            for j in i + 1..big_vertices.len() {
                for k in j + 1..big_vertices.len() {
                    let (a, b, c) = (big_vertices[i], big_vertices[j], big_vertices[k]);
                    let (x, y) = if a < b { (a, b) } else { (b, a) };
                    let (x1, y1) = if a < c { (a, c) } else { (c, a) };
                    let (x2, y2) = if b < c { (b, c) } else { (c, b) };
                    if link.contains_key(&(x, y)) && link.contains_key(&(x1, y1)) && link.contains_key(&(x2, y2)) {
                        let e1 = *link.get(&(x, y)).unwrap();
                        let e2 = *link.get(&(x1, y1)).unwrap();
                        let e3 = *link.get(&(x2, y2)).unwrap();
                        let sum = weight[a] + weight[b] + weight[c];
                        update(sum, e1, e2, e3, a, b, c);
                    }
                }
            }
        }

        // 计算两个三角形的组合权重（并集顶点权重和）
        let combine = |t1: Triangle, t2: Triangle| -> i32 {
            if t1.sum == 0 || t2.sum == 0 {
                return 0;
            }
            let mut total = t1.sum;
            for &v in &t2.nodes {
                if !t1.nodes.contains(&v) {
                    total += weight[v];
                }
            }
            total
        };

        let mut ans = 0;

        // 情况1：共享同一条边的两个三角形
        for e in 0..m {
            if edge_top3[e][0].sum != 0 {
                ans = ans.max(edge_top3[e][0].sum);                     // 单个三角形
                if edge_top3[e][1].sum != 0 {
                    let u = edges[e][0] as usize;
                    let v = edges[e][1] as usize;
                    ans = ans.max(edge_top3[e][0].sum + edge_top3[e][1].sum - weight[u] - weight[v]);
                }
            }
        }

        // 情况2：共享一个顶点的两个三角形
        for v in 0..n {
            if adj[v].is_empty() {
                continue;
            }
            // 找到包含 v 的最大三角形（以边为单位）
            let mut best_edge = edge_of[v][0];
            for &e in &edge_of[v] {
                if edge_top3[e][0].sum > edge_top3[best_edge][0].sum {
                    best_edge = e;
                }
            }
            let best_tri = edge_top3[best_edge][0];
            if best_tri.sum == 0 {
                continue;
            }

            // 与所有包含 v 的三角形组合
            for &tri in &vertex_triangles[v] {
                ans = ans.max(combine(best_tri, tri));
            }

            // 取出 best_tri 中另外两个顶点（相对于 v）
            let other: Vec<usize> = best_tri.nodes.iter().filter(|&&x| x != v).copied().collect();
            if other.len() != 2 {
                continue;
            }
            let p = other[0];
            let q = other[1];

            // 边 (v, p) 和 (v, q) 的索引
            let e1 = *link.get(&(v.min(p), v.max(p))).unwrap();
            let e2 = *link.get(&(v.min(q), v.max(q))).unwrap();

            // 组合这两条边的其他 top 三角形
            for i in 1..3 {
                for j in 1..3 {
                    if edge_top3[e1][i].sum != 0 && edge_top3[e2][j].sum != 0 {
                        ans = ans.max(combine(edge_top3[e1][i], edge_top3[e2][j]));
                    }
                }
            }
        }

        ans
    }
}
```
