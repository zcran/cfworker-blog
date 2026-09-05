---
title: "leetcode-二叉树23"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的坡度

给你一个二叉树的根节点 root ，计算并返回 整个树 的坡度 。

一个树的 节点的坡度 定义即为，该节点左子树的节点之和和右子树节点之和的 差的绝对值 。如果没有左子树的话，左子树的节点之和为 0 ；没有右子树的话也是一样。空结点的坡度是 0 。

整个树 的坡度就是其所有节点的坡度之和。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回整棵树的坡度（所有节点坡度之和）
    ///
    /// 每个节点的坡度 = |左子树节点和 - 右子树节点和|
    /// 后序遍历，返回 (子树坡度之和, 子树节点值之和)
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn find_tilt(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(root).0
    }

    /// 后序遍历，返回 (子树坡度之和, 子树节点值之和)
    fn dfs(node: Option<Rc<RefCell<TreeNode>>>) -> (i32, i32) {
        let Some(node) = node else { return (0, 0) };

        let node = node.borrow();
        let left = Self::dfs(node.left.clone());
        let right = Self::dfs(node.right.clone());

        let tilt = (left.1 - right.1).abs();
        let tilt_sum = left.0 + right.0 + tilt;
        let node_sum = left.1 + right.1 + node.val;

        (tilt_sum, node_sum)
    }
}
```
