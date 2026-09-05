---
title: "leetcode-二叉树33"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树剪枝

给你二叉树的根结点 root ，此外树的每个结点的值要么是 0 ，要么是 1 。

返回移除了所有不包含 1 的子树的原二叉树。

节点 node 的子树为 node 本身加上所有 node 的后代。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 剪枝：移除所有不含 1 的子树
    ///
    /// 后序遍历，递归剪枝左右子树。
    /// 当前节点值为 0 且左右子树都被剪空时，当前节点也被剪除。
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn prune_tree(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let Some(node) = root else { return None };

        let mut n = node.borrow_mut();
        n.left = Self::prune_tree(n.left.take());
        n.right = Self::prune_tree(n.right.take());

        let has_one = n.val == 1 || n.left.is_some() || n.right.is_some();
        drop(n);

        if has_one { Some(node) } else { None }
    }
}
```
