---
title: "Leetcode 强连通分量2"
date: 2023-12-19T15:20:40+08:00
tags: ["leetcode", "强连通分量"]
draft: false
---

## 找到最小生成树里的关键边和伪关键边

给你一个 n 个点的带权无向连通图，节点编号为 0 到 n-1 ，同时还有一个数组 edges ，其中 edges[i] = [from⌄i, to⌄i, weight⌄i] 表示在 from⌄i 和 to⌄i 节点之间有一条带权无向边。最小生成树 (MST) 是给定图中边的一个子集，它连接了所有节点且没有环，而且这些边的权值和最小。

请你找到给定图中最小生成树的所有关键边和伪关键边。如果从图中删去某条边，会导致最小生成树的权值和增加，那么我们就说它是一条关键边。伪关键边则是可能会出现在某些最小生成树中但不会出现在所有最小生成树中的边。

请注意，你可以分别以任意顺序返回关键边的下标和伪关键边的下标。

```
impl Solution {
    pub fn find_critical_and_pseudo_critical_edges(n: i32, edges: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let mut edges: Vec<Vec<_>> = edges
            .into_iter()
            .enumerate()
            .map(|(i, mut x)| {
                x.push(i as i32);
                x.into_iter().map(|e| e as usize).collect()
            })
            .collect();
        edges.sort_by_key(|x| x[2]);

        let n = n as usize;
        let m = edges.len();
        let mut label = vec![1; m];
        let mut dsu = DSU::new(n);

        let mut i = 0;
        while i < edges.len() {
            let mut j = i + 1;
            while j < m && edges[j][2] == edges[i][2] {
                j += 1;
            }

            let mut gn = 0;
            let mut comp_to_id = std::collections::HashMap::new();
            for e in &edges[i..j] {
                let (x, y) = (dsu.find(e[0]), dsu.find(e[1]));
                if x == y {
                    label[e[3]] = 2;
                } else {
                    if comp_to_id.get(&x).is_none() {
                        comp_to_id.insert(x, gn);
                        gn += 1;
                    }
                    if comp_to_id.get(&y).is_none() {
                        comp_to_id.insert(y, gn);
                        gn += 1;
                    }
                }
            }

            let mut graph = vec![vec![]; gn];
            let mut graph_id = vec![vec![]; gn];
            for e in &edges[i..j] {
                let (x, y) = (dsu.find(e[0]), dsu.find(e[1]));
                let id = e[3];
                if x != y {
                    let (xi, yi) = (comp_to_id[&x], comp_to_id[&y]);
                    graph[xi].push(yi);
                    graph[yi].push(xi);
                    graph_id[xi].push(id);
                    graph_id[yi].push(id);
                }
            }

            for b in TarjanSCC::new(graph, graph_id).bridges {
                label[b] = 0;
            }

            for e in &edges[i..j] {
                dsu.unite(e[0], e[1]);
            }
            i = j;
        }

        let mut ans = vec![vec![]; 2];
        for i in 0..label.len() {
            if label[i] < 2 {
                ans[label[i]].push(i as i32);
            }
        }
        ans
    }
}

struct TarjanSCC {
    timestamp: usize,
    low: Vec<usize>,
    dfn: Vec<usize>,
    graph: Vec<Vec<usize>>,
    graph_id: Vec<Vec<usize>>,
    bridges: Vec<usize>,
}

impl TarjanSCC {
    fn new(graph: Vec<Vec<usize>>, graph_id: Vec<Vec<usize>>) -> Self {
        let n = graph.len();
        let mut t = Self {
            timestamp: 1,
            low: vec![0; n],
            dfn: vec![0; n],
            bridges: vec![],
            graph,
            graph_id,
        };
        t.find_bridges();
        t
    }

    fn find_bridges(&mut self) {
        for i in 0..self.graph.len() {
            self.dfs(i, usize::MAX);    
        }
    }

    fn dfs(&mut self, cur: usize, cur_edge: usize) {
        self.dfn[cur] = self.timestamp;
        self.low[cur] = self.timestamp;
        self.timestamp += 1;

        for i in 0..self.graph[cur].len() {
            let nxt_edge = self.graph_id[cur][i];
            if nxt_edge == cur_edge {
                continue;
            }
            let nxt = self.graph[cur][i];
            if self.dfn[nxt] == 0 {
                self.dfs(nxt, nxt_edge);
                if self.dfn[cur] < self.low[nxt] {
                    self.bridges.push(nxt_edge);
                }
            }
            self.low[cur] = self.low[cur].min(self.low[nxt]);
        }
    }
}

struct DSU {
    par: Vec<usize>,
    sz: Vec<usize>,
}

impl DSU {
    fn new(n: usize) -> Self {
        Self {
            par: (0..n).collect(),
            sz: vec![1; n],
        }
    }

    fn find(&mut self, x: usize) -> usize {
        if self.par[x] != x {
            self.par[x] = self.find(self.par[x]);
        }
        self.par[x]
    }

    fn unite(&mut self, x: usize, y: usize) -> bool {
        let (mut px, mut py) = (self.find(x), self.find(y));
        if px != py {
            if self.sz[px] > self.sz[py] {
                std::mem::swap(&mut px, &mut py);
            }
            self.par[px] = self.par[py];
            self.sz[py] += self.sz[px];
            true
        } else {
            false
        }
    }

    fn connected(&mut self, x: usize, y: usize) -> bool {
        self.find(x) == self.find(y)
    }

    fn component_num(&self) -> usize {
        self.par
            .iter()
            .enumerate()
            .filter(|&(x, p)| x == *p)
            .count()
    }
}
```