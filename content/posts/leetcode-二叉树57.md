---
title: "leetcode-二叉树57"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 删除给定值的叶子节点

给你一棵以 root 为根的二叉树和一个整数 target ，请你删除所有值为 target 的 叶子节点 。

注意，一旦删除值为 target 的叶子节点，它的父节点就可能变成叶子节点；如果新叶子节点的值恰好也是 target ，那么这个节点也应该被删除。

也就是说，你需要重复此过程直到不能继续删除。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 后序遍历：先递归处理左右子树，再检查当前节点是否变成值为 target 的叶子节点。
    /// 若是，则删除（返回 None）；否则保留。
    pub fn remove_leaf_nodes(
        root: Option<Rc<RefCell<TreeNode>>>,
        target: i32,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        let Some(node) = root else { return None };
        let mut n = node.borrow_mut();

        // 先递归处理子树
        let left = n.left.take();
        let right = n.right.take();
        n.left = Self::remove_leaf_nodes(left, target);
        n.right = Self::remove_leaf_nodes(right, target);

        // 若当前节点成为值为 target 的叶子，则删除
        if n.val == target && n.left.is_none() && n.right.is_none() {
            None
        } else {
            drop(n);
            Some(node)
        }
    }
}
```
