---
title: "leetcode-二叉树90"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树剪枝

给定一个二叉树 根节点 root ，树的每个节点的值要么是 0，要么是 1。请剪除该二叉树中所有节点的值为 0 的子树。

节点 node 的子树为 node 本身，以及所有 node 的后代。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 剪除二叉树中所有值为 0 的子树。
    ///
    /// 后序遍历：先递归处理左右子树，再判断当前节点是否需要剪除。
    /// 当前节点值为 0 且左右子树均为空（已被剪除）时，当前子树也应被剪除。
    pub fn prune_tree(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let node = root?;
        let mut n = node.borrow_mut();

        n.left = Self::prune_tree(n.left.take());
        n.right = Self::prune_tree(n.right.take());

        // 当前节点值为 0 且左右子树均已剪除 → 剪除当前子树
        if n.val == 0 && n.left.is_none() && n.right.is_none() {
            None
        } else {
            drop(n);
            Some(node)
        }
    }
}
```
