---
title: "leetcode-二叉树11"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 求根节点到叶节点数字之和

给你一个二叉树的根节点 root ，树中每个节点都存放有一个 0 到 9 之间的数字。
每条从根节点到叶节点的路径都代表一个数字：

例如，从根节点到叶节点的路径 1 -> 2 -> 3 表示数字 123 。

计算从根节点到叶节点生成的 所有数字之和 。

叶节点 是指没有子节点的节点。




```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回从根到所有叶节点路径表示的数字之和
    pub fn sum_numbers(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(&root, 0)
    }

    /// 递归遍历，x 为从根到当前父节点的路径数值
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, x: i32) -> i32 {
        if let Some(node) = node {
            let node = node.borrow();
            let x = x * 10 + node.val;
            // 叶子节点：返回完整路径数字
            if node.left.is_none() && node.right.is_none() {
                return x;
            }
            Self::dfs(&node.left, x) + Self::dfs(&node.right, x)
        } else {
            0
        }
    }
}
```
