---
title: "leetcode-位掩码37"
date: 2026-05-18T10:28:00+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 最大好子树分数


给你一个根节点为 0 的无向树，包含 n 个节点，编号从 0 到 n - 1。每个节点 i 都有一个整数值 vals[i]，其父节点为 par[i] 。

从一个节点 子树 内选取部分节点，它们的数值组成一个 子集 ，如果所选数值的十进制表示中，从 0 到 9 每个数字在所有数的数位最多出现一次，那么我们称它是 好 子集。

一个好子集的 分数 是其节点值的总和。

定义一个长度为 n 的数组 maxScore，其中 maxScore[u] 表示以节点 u 为根的子树（包括 u 本身及其所有后代）中，好子集的最大可能值总和。

返回 maxScore 中所有值的总和。

由于答案可能很大，请将其对 10^9 + 7 取模 后返回。

数组的 子集 是选取数组中元素得到的集合（可能为空）。


```
impl Solution {
    /// 计算每个子树中，选取节点值（十进制数字不重复）的最大和，并求和返回模 1e9+7
    pub fn good_subtree_sum(vals: Vec<i32>, par: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        const D: usize = 10;
        let n = vals.len();

        // 构建树（邻接表）
        let mut g = vec![vec![]; n];
        for i in 1..n {
            let p = par[i] as usize;
            g[p].push(i);
        }

        let mut ans = 0i64;

        // 深度优先搜索，返回 f[mask] 表示当前子树内数字集合恰好为 mask 的最大和
        fn dfs(
            x: usize,
            vals: &[i32],
            g: &[Vec<usize>],
            ans: &mut i64,
        ) -> [i64; 1 << D] {
            // 计算当前节点值的数字掩码
            let mut mask = 0u16;
            let mut v = vals[x];
            let mut ok = true;
            while v > 0 {
                let d = (v % 10) as usize;
                if (mask >> d) & 1 == 1 {
                    ok = false;
                    break;
                }
                mask |= 1 << d;
                v /= 10;
            }
            let val = vals[x] as i64;

            // 初始化 f，当前节点选或不选
            let mut f = [0i64; 1 << D];
            if ok && mask != 0 {
                f[mask as usize] = val;
            }

            // 合并子节点的结果（取每个 mask 的最大值）
            for &y in &g[x] {
                let child = dfs(y, vals, g, ans);
                for i in 0..(1 << D) {
                    f[i] = f[i].max(child[i]);
                }
            }

            // 子集合并：枚举所有掩码，尝试组合子集
            for i in 3..(1 << D) {
                // 枚举 i 的非空真子集
                let mut sub = (i - 1) & i;
                while sub > 0 {
                    let other = i ^ sub;
                    f[i] = f[i].max(f[sub] + f[other]);
                    sub = (sub - 1) & i;
                }
            }

            // 记录当前子树的最大和
            let best = f.iter().max().copied().unwrap_or(0);
            *ans = (*ans + best) % MOD;
            f
        }

        dfs(0, &vals, &g, &mut ans);
        ans as i32
    }
}
```
