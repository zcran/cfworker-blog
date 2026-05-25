---
title: "leetcode-位掩码38"
date: 2026-05-18T10:28:00+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 图中的最长回文路径


给你一个整数 n 和一个包含 n 个节点的 无向图 ，节点编号从 0 到 n - 1，以及一个二维数组 edges，其中 edges[i] = [uˇi, vˇi] 表示节点 ui 和节点 vi 之间有一条边。

同时给你一个长度为 n 的字符串 label，其中 label[i] 是与节点 i 关联的字符。

你可以从任意节点开始，移动到任意相邻节点，每个节点 最多 访问一次。

返回通过访问一条路径，路径中 不包含重复 节点，所能形成的 最长回文串 的长度。

回文串 是指正着读和反着读相同的字符串。


```
impl Solution {
    pub fn max_len(n: i32, edges: Vec<Vec<i32>>, label: String) -> i32 {
        let n = n as usize;
        let label = label.as_bytes();
        // 计算理论最大值
        let mut cnt = [0; 26];
        for &ch in label {
            cnt[(ch - b'a') as usize] += 1;
        }
        let odd = cnt.iter().filter(|&&c| c % 2 == 1).count();
        let theoretical_max = n - (odd.saturating_sub(1));

        // 检查是否为完全图（边数 = n*(n-1)/2）
        let total_edges = n * (n - 1) / 2;
        if edges.len() == total_edges {
            return theoretical_max as i32;
        }

        // 构建邻接表
        let mut g = vec![vec![]; n];
        for e in &edges {
            let x = e[0] as usize;
            let y = e[1] as usize;
            g[x].push(y);
            g[y].push(x);
        }

        // 记忆化数组 memo[x][y][mask]，x≤y
        // 由于 n 较小（通常 ≤15），使用 Vec<Vec<Vec<Option<i32>>>> 存储
        let max_mask = 1 << n;
        let mut memo = vec![vec![vec![None; max_mask]; n]; n];

        // 递归函数：返回从 (x, y) 两端还能扩展的节点数（不包括 x,y）
        fn dfs(
            x: usize,
            y: usize,
            vis: usize,
            g: &[Vec<usize>],
            label: &[u8],
            memo: &mut [Vec<Vec<Option<i32>>>],
        ) -> i32 {
            if let Some(v) = memo[x][y][vis] {
                return v;
            }
            let mut best = 0;
            // 枚举从 x 出发的邻居 v
            for &v in &g[x] {
                if (vis >> v) & 1 == 1 {
                    continue;
                }
                // 枚举从 y 出发的邻居 w
                for &w in &g[y] {
                    if (vis >> w) & 1 == 1 || w == v {
                        continue;
                    }
                    if label[w] == label[v] {
                        let (nv, nw) = if v < w { (v, w) } else { (w, v) };
                        let r = dfs(nv, nw, vis | (1 << v) | (1 << w), g, label, memo);
                        best = best.max(r + 2);
                    }
                }
            }
            memo[x][y][vis] = Some(best);
            best
        }

        let mut ans = 0;
        // 奇回文：中心节点 x
        for x in 0..n {
            let cur = dfs(x, x, 1 << x, &g, &label, &mut memo) + 1;
            ans = ans.max(cur);
            if ans == theoretical_max as i32 {
                return ans;
            }
            // 偶回文：中心边 (x, y)
            for &y in &g[x] {
                if x < y && label[x] == label[y] {
                    let cur = dfs(x, y, (1 << x) | (1 << y), &g, &label, &mut memo) + 2;
                    ans = ans.max(cur);
                    if ans == theoretical_max as i32 {
                        return ans;
                    }
                }
            }
        }
        ans
    }
}
```
