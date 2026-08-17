---
title: "leetcode-二叉树8"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的最小深度

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

说明：叶子节点是指没有子节点的节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉树的最小深度（根到最近叶子节点的最短路径节点数）
    pub fn min_depth(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        match root {
            None => 0,
            Some(node) => {
                let node = node.borrow();
                match (&node.left, &node.right) {
                    // 叶子节点：深度为 1
                    (None, None) => 1,
                    // 只有左子树：最小深度在左子树
                    (Some(_), None) => 1 + Self::min_depth(node.left.clone()),
                    // 只有右子树：最小深度在右子树
                    (None, Some(_)) => 1 + Self::min_depth(node.right.clone()),
                    // 左右子树都有：取较小者
                    (Some(_), Some(_)) => {
                        1 + Self::min_depth(node.left.clone()).min(Self::min_depth(node.right.clone()))
                    }
                }
            }
        }
    }
}
```
