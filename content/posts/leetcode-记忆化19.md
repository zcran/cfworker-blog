---
title: "leetcode-记忆化19"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 将子数组重新排序得到同一个二叉搜索树的方案数

给你一个数组 nums 表示 1 到 n 的一个排列。我们按照元素在 nums 中的顺序依次插入一个初始为空的二叉搜索树（BST）。请你统计将 nums 重新排序后，统计满足如下条件的方案数：重排后得到的二叉搜索树与 nums 原本数字顺序得到的二叉搜索树相同。

比方说，给你 nums = [2,1,3]，我们得到一棵 2 为根，1 为左孩子，3 为右孩子的树。数组 [2,3,1] 也能得到相同的 BST，但 [3,2,1] 会得到一棵不同的 BST 。
请你返回重排 nums 后，与原数组 nums 得到相同二叉搜索树的方案数。

由于答案可能会很大，请将结果对 109 + 7 取余数。

```
use std::collections::HashMap;

// ---------- 并查集结构（路径压缩）----------
struct UnionFind {
    parent: Vec<usize>,
}

impl UnionFind {
    fn new(n: usize) -> Self {
        UnionFind {
            parent: (0..n).collect(), // 初始每个节点自成一派
        }
    }

    /// 查找根节点 + 路径压缩
    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    /// 合并两个集合，返回是否成功合并（原本属于不同集合）
    fn union(&mut self, x: usize, y: usize) -> bool {
        let (root_x, root_y) = (self.find(x), self.find(y));
        if root_x != root_y {
            self.parent[root_y] = root_x;
            true
        } else {
            false
        }
    }
}

// ---------- 主解题结构 ----------
impl Solution {
    /// 计算能够生成相同二叉搜索树（BST）的不同插入顺序的数量。
    /// 题目等价于：给定一个 1..n 的排列 nums（即 BST 的插入顺序），
    /// 问有多少种不同的排列方式（包括原排列本身）构造出的 BST 形状相同？
    /// 最终答案需要减去 1（排除原排列）。
    ///
    /// 算法核心：
    /// 1. 预处理阶乘、逆元及组合数，用于计算“多棵子树混合时的排列数”。
    /// 2. 逆序遍历插入顺序（相当于按照值的大小构建 BST，并合并相邻区间）。
    /// 3. 使用并查集维护已经“出现”的节点形成的连续区域。
    /// 4. 每当插入一个新节点，它会连接左右相邻的已存在区域（子树），
    ///    新节点的方案数 = 左右子树方案数的乘积 × 组合数(左右子树总大小, 左子树大小)。
    /// 5. 最终根节点（nums[0]）对应的方案数即为所有可能的排列数，再减去 1。
    pub fn num_of_ways(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();
        // 当节点数 <= 2 时，只有原排列一种顺序（减去 1 后为 0）
        if n <= 2 {
            return 0;
        }

        // ---------- 预处理阶乘、逆元和组合数 ----------
        // 函数式初始化向量
        let mut fact = vec![1i64; n];
        let mut inv_fact = vec![1i64; n];
        let mut inv = vec![1i64; n]; // 逆元数组

        for i in 2..n {
            fact[i] = fact[i - 1] * (i as i64) % MOD;
            inv[i] = inv[MOD as usize % i] * (MOD - MOD / i as i64) % MOD;
            inv_fact[i] = inv_fact[i - 1] * inv[i] % MOD;
        }

        // 闭包形式的组合数计算
        let comb = |n: usize, k: usize| -> i64 {
            if k > n {
                0
            } else {
                fact[n] * inv_fact[k] % MOD * inv_fact[n - k] % MOD
            }
        };

        // ---------- 主逻辑：自底向上合并 ----------
        let mut uf = UnionFind::new(n);
        let mut subtree_size = vec![0usize; n];    // 每个节点所在子树的总结点数
        let mut ways = vec![1i64; n];              // 每个节点子树的排列方案数

        // 逆序遍历插入序列（相当于按值从大到小处理，但原代码已经这样做）
        for &val in nums.iter().rev() {
            let idx = (val - 1) as usize;          // 转换为 0 基索引
            let mut total_size = 0;                // 左右子树总节点数（不含当前节点）
            let mut product = 1i64;                // 左右子树方案数的乘积
            let mut left_root = n;                 // 左子树根节点的集合代表

            // 检查左侧是否有已存在的节点（且已处理过，即子树大小 > 0）
            if idx > 0 && subtree_size[idx - 1] > 0 {
                left_root = uf.find(idx - 1);
                total_size += subtree_size[left_root];
                product = product * ways[left_root] % MOD;
                uf.union(idx, left_root);          // 合并到当前节点
            }

            // 检查右侧是否有已存在的节点
            if idx + 1 < n && subtree_size[idx + 1] > 0 {
                let right_root = uf.find(idx + 1);
                total_size += subtree_size[right_root];
                product = product * ways[right_root] % MOD;
                uf.union(idx, right_root);
            }

            // 如果存在左子树（或右子树），需要计算混合排列数
            if left_root != n {
                // 左子树大小（在 total_size 中已知为 left_size）
                let left_size = subtree_size[left_root];
                product = product * comb(total_size, left_size) % MOD;
            }

            // 记录当前节点所在区域的大小和方案数
            ways[idx] = product;
            subtree_size[idx] = 1 + total_size;
        }

        // 根节点（原数组的第一个元素）的方案数即为所有不同排列的总数
        // 题目要求排除原始排列本身，所以需要减 1
        let root_idx = (nums[0] - 1) as usize;
        ((ways[root_idx] + MOD - 1) % MOD) as i32
    }
}

```
