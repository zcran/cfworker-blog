---
title: "leetcode-二叉树52"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 最深叶节点的最近公共祖先

给你一个有根节点 root 的二叉树，返回它 最深的叶节点的最近公共祖先 。

回想一下：

叶节点 是二叉树中没有子节点的节点
树的根节点的 深度 为 0，如果某一节点的深度为 d，那它的子节点的深度就是 d+1
如果我们假定 A 是一组节点 S 的 最近公共祖先，S 中的每个节点都在以 A 为根节点的子树中，且 A 的深度达到此条件下可能的最大值。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回最深叶节点的最近公共祖先。
    ///
    /// 后序遍历，每个节点返回一个元组：
    /// (以当前节点为根的子树的最大深度, 该子树内最深叶节点的 LCA)。
    ///
    /// - 若左右子树最大深度相同，说明最深叶节点分布在两侧，当前节点即为 LCA
    /// - 若左子树更深，LCA 在左子树
    /// - 若右子树更深，LCA 在右子树
    pub fn lca_deepest_leaves(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        Self::dfs(&root).1
    }

    /// 返回 (子树最大深度, 该子树内最深叶节点的 LCA)。
    /// 空节点深度为 -1，表示不存在叶节点。
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>) -> (i32, Option<Rc<RefCell<TreeNode>>>) {
        let Some(n) = node else { return (-1, None) };
        let n = n.borrow();

        let (left_depth, left_lca) = Self::dfs(&n.left);
        let (right_depth, right_lca) = Self::dfs(&n.right);

        if left_depth == right_depth {
            // 左右子树深度相同，当前节点是 LCA
            (left_depth + 1, node.clone())
        } else if left_depth > right_depth {
            // 左子树更深
            (left_depth + 1, left_lca)
        } else {
            // 右子树更深
            (right_depth + 1, right_lca)
        }
    }
}
```
