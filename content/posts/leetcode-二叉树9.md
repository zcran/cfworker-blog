---
title: "leetcode-二叉树9"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 路径总和

给你二叉树的根节点 root 和一个表示目标和的整数 targetSum 。判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum 。如果存在，返回 true ；否则，返回 false 。

叶子节点 是指没有子节点的节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 判断是否存在根节点到叶子节点的路径，其节点值之和等于 target_sum
    pub fn has_path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> bool {
        match root {
            None => false,
            Some(node) => {
                let node = node.borrow();
                let remain = target_sum - node.val;
                // 叶子节点：检查剩余值是否恰好为 0
                if node.left.is_none() && node.right.is_none() {
                    return remain == 0;
                }
                Self::has_path_sum(node.left.clone(), remain)
                    || Self::has_path_sum(node.right.clone(), remain)
            }
        }
    }
}
```
