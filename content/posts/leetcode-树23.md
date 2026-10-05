---
title: "leetcode-树23"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 统计有根树中不相邻子集的数目

给你一棵有 n 个节点的有根树，节点编号从 0 到 n - 1 ，由一个长度为 n 的整数数组 parent 表示，其中：

parent[0] = -1 （节点 0 是根节点）。
对于每个 1 <= i < n ，parent[i] 是节点 i 的父节点（0 <= parent[i] < i）。

另外给你一个长度为 n 的整数数组 nums ，其中 nums[i] 是节点 i 的值，以及一个整数 k。

如果节点的一个非空子集满足以下条件，则称为 有效 子集：

所选节点的值之 和 可以被 k 整除 。
所选节点中没有 两 个节点在树中是 相邻 的（即没有节点及其直接父节点同时包含在子集中）。

返回有效子集的数量对 10^9 + 7 取余的结果。


```
impl Solution {
    /// 树形 DP（树上独立集 + 模 k 背包）
    ///
    /// 状态定义：
    ///   f0[i]：不选当前节点 x 时，子树 x 中选出的点集和模 k 为 i 的方案数
    ///   f1[i]：选当前节点 x 时，子树 x 中选出的点集和模 k 为 i 的方案数
    ///
    /// 转移：
    ///   对于 x 的每个子节点 y（先递归得到 fy0, fy1）：
    ///     - 不选 x：y 可选可不选，合并 fy0 + fy1 到 f0
    ///     - 选 x  ：y 不能选，只能合并 fy0 到 f1
    ///
    /// 最终答案 = (f0[0] + f1[0] - 1) mod MOD，减去空集。
    pub fn count_valid_subsets(parent: Vec<i32>, nums: Vec<i32>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = parent.len();
        let k = k as usize;

        // 建树：children[u] 存储 u 的所有子节点
        let mut children = vec![Vec::new(); n];
        for i in 1..n {
            children[parent[i] as usize].push(i);
        }

        // 迭代后序遍历，避免递归深度过大
        // 第一遍：生成后序顺序
        let mut order = Vec::with_capacity(n);
        let mut stack = vec![0usize];
        while let Some(u) = stack.pop() {
            order.push(u);
            for &v in &children[u] {
                stack.push(v);
            }
        }

        // dp0[u][i], dp1[u][i]：不选 / 选 u 时，子树 u 中点权和模 k 为 i 的方案数
        let mut dp0 = vec![vec![0i64; k]; n];
        let mut dp1 = vec![vec![0i64; k]; n];

        // 逆序处理（自底向上）
        for &u in order.iter().rev() {
            // 初始化：空集和为 0；选 u 时初始和 = nums[u] % k
            dp0[u][0] = 1;
            dp1[u][nums[u] as usize % k] = 1;

            for &v in &children[u] {
                let fy0 = &dp0[v];
                let fy1 = &dp1[v];

                // 合并到 f0：y 可选可不选
                let mut nf0 = vec![0i64; k];
                for i in 0..k {
                    let v_sum = (fy0[i] + fy1[i]) % MOD;
                    if v_sum == 0 {
                        continue;
                    }
                    for j in 0..k {
                        let s = (i + j) % k;
                        nf0[s] = (nf0[s] + v_sum * dp0[u][j]) % MOD;
                    }
                }

                // 合并到 f1：y 不能选
                let mut nf1 = vec![0i64; k];
                for i in 0..k {
                    if fy0[i] == 0 {
                        continue;
                    }
                    for j in 0..k {
                        let s = (i + j) % k;
                        nf1[s] = (nf1[s] + fy0[i] * dp1[u][j]) % MOD;
                    }
                }

                dp0[u] = nf0;
                dp1[u] = nf1;
            }
        }

        // 答案 = f0[0] + f1[0] - 1（减去空集）
        let ans = (dp0[0][0] + dp1[0][0] - 1 + MOD) % MOD;
        ans as i32
    }
}
```
