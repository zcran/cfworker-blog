---
title: "leetcode-二叉树58"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 分裂二叉树的最大乘积

给你一棵二叉树，它的根为 root 。请你删除 1 条边，使二叉树分裂成两棵子树，且它们子树和的乘积尽可能大。

由于答案可能会很大，请你将结果对 10^9 + 7 取模后再返回。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 先求整棵树的总和 total，再后序遍历每棵子树。
    /// 对于子树和 cur，分割乘积为 cur * (total - cur)，取最大值。
    pub fn max_product(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        let total = Self::tree_sum(&root);
        let mut best: i64 = 0;
        Self::dfs(&root, total, &mut best);

        (best % MOD) as i32
    }

    /// 计算以 node 为根的子树所有节点值之和。
    fn tree_sum(node: &Option<Rc<RefCell<TreeNode>>>) -> i64 {
        let Some(n) = node else { return 0 };
        let n = n.borrow();
        n.val as i64 + Self::tree_sum(&n.left) + Self::tree_sum(&n.right)
    }

    /// 后序遍历，返回当前子树和，同时用当前子树和更新最大分割乘积。
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, total: i64, best: &mut i64) -> i64 {
        let Some(n) = node else { return 0 };
        let n = n.borrow();

        let cur = n.val as i64
            + Self::dfs(&n.left, total, best)
            + Self::dfs(&n.right, total, best);

        *best = (*best).max(cur * (total - cur));
        cur
    }
}
```
