---
title: "Leetcode 双连通分量1"
date: 2023-12-19T15:12:57+08:00
tags: ["leetcode", "双连通分量"]
draft: false
---

## 查找集群内的关键连接

数据中心有 n 台服务器，分别按从 0 到 n-1 的方式进行了编号。它们之间以 服务器到服务器 的形式相互连接组成了一个内部集群，连接是无向的。用  connections 表示集群网络，connections[i] = [a, b] 表示服务器 a 和 b 之间形成连接。任何服务器都可以直接或者间接地通过网络到达任何其他服务器。

关键连接 是在该集群中的重要连接，假如我们将它移除，便会导致某些服务器无法访问其他服务器。

请你以任意顺序返回该集群内的所有 关键连接 。

```
use std::iter::repeat;
static mut DFN: [i32; 100010] = [0; 100010];
static mut LOW: [i32; 100010] = [0; 100010];
static mut DFN_CNT: i32 = 0;
impl Solution {
    pub fn critical_connections(n: i32, connections: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        return unsafe{Solution::critical_connections2(n,connections)};
    }
    pub unsafe fn critical_connections2(n: i32, connections: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let mut graph: Vec<Vec<i32>> = repeat(vec![]).take(n as usize).collect();
        for edge in connections.iter() {
            graph[edge[0] as usize].push(edge[1]);
            graph[edge[1] as usize].push(edge[0]);
        }
        for i in 0..n {
            DFN[i as usize] = 0;
            LOW[i as usize] = 0;
        }

        DFN_CNT = 0;
        let mut ans: Vec<Vec<i32>> = vec![];
        Solution::tarjan(0, -1, &mut graph, &mut ans);
        return ans;
    }

    // tarjan  dfs过程
    // 点的编号是cur，dfn X low X
    unsafe fn tarjan(cur: i32, father: i32, graph: &mut Vec<Vec<i32>>, ans: &mut Vec<Vec<i32>>) {
        // 第一次来到cur
        // 分配dfn、low序号
        DFN_CNT += 1;
        LOW[cur as usize] = DFN_CNT;
        DFN[cur as usize] = LOW[cur as usize];
        let n = graph[cur as usize].len() as i32;
        for j in 0..n {
            let next = graph[cur as usize][j as usize];
            if next != father {
                if DFN[next as usize] == 0 {
                    // 下级的节点没跑过，就去跑
                    Solution::tarjan(next, cur, graph, ans);
                    LOW[cur as usize] = get_min(LOW[cur as usize], LOW[next as usize]);
                } else {
                    // 下级的节点跑过了，直接更新low
                    LOW[cur as usize] = get_min(LOW[cur as usize], DFN[next as usize]);
                }
            }
        }
        if LOW[cur as usize] == DFN[cur as usize] && cur != 0 {
            ans.push(vec![father, cur]);
        }
    }
}

fn get_min<T: Clone + Copy + std::cmp::PartialOrd>(a: T, b: T) -> T {
    if a < b {
        a
    } else {
        b
    }
}
```