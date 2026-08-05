---
title: "leetcode-设计20"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 树节点的第 K 个祖先

给你一棵树，树上有 n 个节点，按从 0 到 n-1 编号。树以父节点数组的形式给出，其中 parent[i] 是节点 i 的父节点。树的根节点是编号为 0 的节点。

树节点的第 k 个祖先节点是从该节点到根节点路径上的第 k 个节点。

实现 TreeAncestor 类：

TreeAncestor（int n， int[] parent） 对树和父数组中的节点数初始化对象。

getKthAncestor(int node, int k) 返回节点 node 的第 k 个祖先节点。如果不存在这样的祖先节点，返回 -1 。


```
/// 树祖先查询：使用二进制提升（倍增）法
struct TreeAncestor {
    /// dp[i][j] 表示节点 i 的第 2^j 个祖先
    /// 如果不存在，则为 -1
    dp: Vec<Vec<i32>>,
    /// 最大幂次（log2(n) + 1）
    max_pow: usize,
}

impl TreeAncestor {
    /// 初始化树祖先数据结构
    ///
    /// # 参数
    /// - `n`: 节点总数
    /// - `parent`: 父节点数组，parent[i] 是节点 i 的父节点，根节点 parent[0] = -1
    ///
    /// 时间复杂度: O(n log n)，空间复杂度: O(n log n)
    pub fn new(n: i32, parent: Vec<i32>) -> Self {
        let n_usize = n as usize;

        // 计算最大幂次：使 2^max_pow > n
        let max_pow = (0..32)
            .find(|&p| (1 << p) > n)
            .unwrap_or(31) as usize;

        // 初始化 dp 表，填充 -1 表示不存在
        let mut dp = vec![vec![-1; max_pow]; n_usize];

        // 初始化第 0 层：直接父节点
        for (i, &p) in parent.iter().enumerate() {
            dp[i][0] = p;
        }

        // 动态规划：计算第 2^j 个祖先
        for j in 1..max_pow {
            for i in 0..n_usize {
                let mid = dp[i][j - 1];
                if mid != -1 {
                    dp[i][j] = dp[mid as usize][j - 1];
                }
                // 否则保持 -1
            }
        }

        Self { dp, max_pow }
    }

    /// 获取节点 node 的第 k 个祖先
    ///
    /// # 参数
    /// - `node`: 起始节点
    /// - `k`: 向上走的步数
    ///
    /// # 返回
    /// 第 k 个祖先节点，如果不存在则返回 -1
    ///
    /// 时间复杂度: O(log n)，空间复杂度: O(1)
    pub fn get_kth_ancestor(&self, mut node: i32, mut k: i32) -> i32 {
        // 如果 k 为 0，返回节点本身
        if k == 0 {
            return node;
        }

        let mut bit = 0;

        // 按二进制位处理 k，但不超过 max_pow
        while k > 0 && node != -1 && bit < self.max_pow {
            if k & 1 == 1 {
                // 如果当前位为 1，跳转到对应的祖先
                node = self.dp[node as usize][bit];
                if node == -1 {
                    return -1;
                }
            }
            k >>= 1;
            bit += 1;
        }

        // 如果 k 还有剩余位但已经处理完所有幂次，检查是否还有更深的祖先
        // 实际上，如果 k 的位数超过 max_pow，无法找到更深祖先
        node
    }
}
```
