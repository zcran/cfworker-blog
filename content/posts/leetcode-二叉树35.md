---
title: "leetcode-二叉树35"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 具有所有最深节点的最小子树

给定一个根为 root 的二叉树，每个节点的深度是 该节点到根的最短距离 。

返回包含原始树中所有 最深节点 的 最小子树 。

如果一个节点在 整个树 的任意节点之间具有最大的深度，则该节点是 最深的 。

一个节点的 子树 是该节点加上它的所有后代的集合。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回包含所有最深节点的最小子树
    ///
    /// 后序遍历，返回 (子树最大深度, 该子树的最小公共祖先)。
    /// 左右深度相等时当前节点就是 LCA，否则取更深一侧的 LCA。
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn subtree_with_all_deepest(
        root: Option<Rc<RefCell<TreeNode>>>,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        Self::dfs(&root).1
    }

    /// 返回 (子树最大深度, 包含所有最深节点的最小子树)
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>) -> (i32, Option<Rc<RefCell<TreeNode>>>) {
        let Some(node) = node else { return (0, None) };

        let n = node.borrow();
        let (left_depth, left_lca) = Self::dfs(&n.left);
        let (right_depth, right_lca) = Self::dfs(&n.right);

        match left_depth.cmp(&right_depth) {
            std::cmp::Ordering::Greater => (left_depth + 1, left_lca),
            std::cmp::Ordering::Less => (right_depth + 1, right_lca),
            std::cmp::Ordering::Equal => (left_depth + 1, Some(node.clone())),
        }
    }
}
```
