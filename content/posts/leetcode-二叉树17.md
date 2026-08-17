---
title: "leetcode-二叉树17"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 左叶子之和

给定二叉树的根节点 root ，返回所有左叶子之和。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉树中所有左叶子节点的值之和
    pub fn sum_of_left_leaves(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(&root, false)
    }

    /// is_left: 当前节点是否为父节点的左子节点
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, is_left: bool) -> i32 {
        let node = match node {
            Some(n) => n.borrow(),
            None => return 0,
        };

        // 叶子节点且是左子节点 → 返回其值
        if node.left.is_none() && node.right.is_none() {
            return if is_left { node.val } else { 0 };
        }

        // 非叶子节点 → 递归左右子树
        Self::dfs(&node.left, true) + Self::dfs(&node.right, false)
    }
}
```
