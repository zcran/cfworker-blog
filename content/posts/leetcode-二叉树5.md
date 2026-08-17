---
title: "leetcode-二叉树5"
date: 2026-08-15T11:32:27+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的最大深度

给定一个二叉树 root ，返回其最大深度。

二叉树的 最大深度 是指从根节点到最远叶子节点的最长路径上的节点数。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉树的最大深度（从根到最远叶子节点的节点数）
    pub fn max_depth(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        root.map_or(0, |node| {
            let node = node.borrow();
            1 + Self::max_depth(node.left.clone()).max(Self::max_depth(node.right.clone()))
        })
    }
}
```
