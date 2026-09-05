---
title: "leetcode-二叉树22"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


##  二叉树的直径

给你一棵二叉树的根节点，返回该树的 直径 。

二叉树的 直径 是指树中任意两个节点之间最长路径的 长度 。这条路径可能经过也可能不经过根节点 root 。

两节点之间路径的 长度 由它们之间边数表示。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回二叉树的直径（任意两节点间最长路径的边数）
    ///
    /// 后序遍历，对每个节点计算左右子树高度之和，取全局最大。
    /// 时间复杂度 O(n)，空间复杂度 O(h)，h 为树高。
    pub fn diameter_of_binary_tree(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut diameter = 0;
        Self::height(&root, &mut diameter);
        diameter
    }

    /// 返回以该节点为根的子树高度，同时更新全局直径
    fn height(node: &Option<Rc<RefCell<TreeNode>>>, diameter: &mut i32) -> i32 {
        let Some(node) = node else { return 0 };

        let node = node.borrow();
        let left = Self::height(&node.left, diameter);
        let right = Self::height(&node.right, diameter);

        *diameter = (*diameter).max(left + right);
        left.max(right) + 1
    }
}
```
