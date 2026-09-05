---
title: "leetcode-二叉树62"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树中的伪回文路径

给你一棵二叉树，每个节点的值为 1 到 9 。我们称二叉树中的一条路径是 「伪回文」的，当它满足：路径经过的所有节点值的排列中，存在一个回文序列。

请你返回从根到叶子节点的所有路径中 伪回文 路径的数目。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn pseudo_palindromic_paths(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(root.as_ref(), 0)
    }

    /// 用位掩码记录 1~9 各数字出现次数的奇偶性（第 i 位为 1 表示数字 i 出现了奇数次）
    /// 伪回文条件：路径中最多只有一个数字出现奇数次 → 位掩码中 1 的个数 ≤ 1
    fn dfs(node: Option<&Rc<RefCell<TreeNode>>>, mask: i32) -> i32 {
        let Some(n) = node else { return 0 };
        let n = n.borrow();
        let mask = mask ^ (1 << n.val); // 翻转当前数字对应的奇偶位

        // 叶子节点：检查位掩码是否为 0 或 2 的幂次（即只有一个 1 或没有 1）
        if n.left.is_none() && n.right.is_none() {
            return if mask & (mask - 1) == 0 { 1 } else { 0 };
        }

        Self::dfs(n.left.as_ref(), mask) + Self::dfs(n.right.as_ref(), mask)
    }
}
```
