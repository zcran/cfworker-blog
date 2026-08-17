---
title: "leetcode-二叉树14"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 翻转二叉树

给你一棵二叉树的根节点 root ，翻转这棵二叉树，并返回其根节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 翻转二叉树：递归交换每个节点的左右子树
    pub fn invert_tree(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        if let Some(node) = root {
            let mut n = node.borrow_mut();
            let left = n.left.take();
            let right = n.right.take();
            n.left = Self::invert_tree(right);
            n.right = Self::invert_tree(left);
            drop(n);
            Some(node)
        } else {
            None
        }
    }
}
```
