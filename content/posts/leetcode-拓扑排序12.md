---
title: "leetcode-拓扑排序12"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 统计为蚁群构筑房间的不同顺序

你是一只蚂蚁，负责为蚁群构筑 n 间编号从 0 到 n-1 的新房间。给你一个 下标从 0 开始 且长度为 n 的整数数组 prevRoom 作为扩建计划。其中，prevRoom[i] 表示在构筑房间 i 之前，你必须先构筑房间 prevRoom[i] ，并且这两个房间必须 直接 相连。房间 0 已经构筑完成，所以 prevRoom[0] = -1 。扩建计划中还有一条硬性要求，在完成所有房间的构筑之后，从房间 0 可以访问到每个房间。

你一次只能构筑 一个 房间。你可以在 已经构筑好的 房间之间自由穿行，只要这些房间是 相连的 。如果房间 prevRoom[i] 已经构筑完成，那么你就可以构筑房间 i。

返回你构筑所有房间的 不同顺序的数目 。由于答案可能很大，请返回对 109 + 7 取余 的结果。

```
use lazy_static::lazy_static;

const N: usize = 100_001;          // 最大房间数
const MOD: i64 = 1_000_000_007;    // 模数（质数）

// 预计算阶乘和阶乘的模逆元（费马小定理）
lazy_static! {
    /// fact[i] = i! % MOD
    static ref FACT: [i64; N] = {
        let mut fact = [1; N];
        for i in 1..N {
            fact[i] = fact[i-1] * i as i64 % MOD;
        }
        fact
    };
    /// inv_fact[i] = (i!)^(-1) % MOD
    static ref INV_FACT: [i64; N] = {
        let mut inv_fact = [1; N];
        // 先计算最大值的逆元 (N-1)! 的逆
        inv_fact[N-1] = mod_pow(FACT[N-1], MOD - 2);
        // 递推计算所有逆元: inv_fact[i-1] = inv_fact[i] * i % MOD
        for i in (1..N).rev() {
            inv_fact[i-1] = inv_fact[i] * i as i64 % MOD;
        }
        inv_fact
    };
}

/// 快速幂：a^b % MOD
fn mod_pow(mut a: i64, mut b: i64) -> i64 {
    let mut res = 1;
    while b > 0 {
        if b & 1 == 1 {
            res = res * a % MOD;
        }
        a = a * a % MOD;
        b >>= 1;
    }
    res
}

impl Solution {
    /// 返回满足依赖关系的房间建造顺序的种数（LeetCode 1916）。
    /// 本质是求树的拓扑排序数量，公式为：对于每个节点，其子树的方案数乘以多重组合数。
    pub fn ways_to_build_rooms(prev_room: Vec<i32>) -> i32 {
        let n = prev_room.len();
        // 构建邻接表（有根树，根为 0）
        let mut children = vec![Vec::new(); n];
        for (i, &p) in prev_room.iter().enumerate().skip(1) { // 房间0没有前驱（prev_room[0] = -1）
            children[p as usize].push(i);
        }

        // 深度优先搜索（后序遍历），返回 (子树大小, 方案数)
        fn dfs(u: usize, children: &[Vec<usize>]) -> (usize, i64) {
            let mut size = 1;      // 当前节点自身占一个位置
            let mut ways = 1;      // 初始方案数为1

            for &v in &children[u] {
                let (sub_size, sub_ways) = dfs(v, children);
                // 组合数学公式：
                // ways = ways * sub_ways * C(当前已累积总大小 + sub_size, sub_size)
                // 等价于累乘 inv_fact[sub_size]，最后统一乘 fact[总大小]
                ways = ways * sub_ways % MOD;
                ways = ways * INV_FACT[sub_size] % MOD;
                size += sub_size;
            }
            // 所有子节点处理完毕，当前子树总大小为 size，
            // 需要将 (size-1)! 乘入（即把当前节点放在所有子节点之前）
            ways = ways * FACT[size - 1] % MOD;
            (size, ways)
        }

        let (_, ans) = dfs(0, &children);
        ans as i32
    }
}
```
