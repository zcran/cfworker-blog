---
title: "leetcode-二叉树49"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 根到叶路径上的不足节点

给你二叉树的根节点 root 和一个整数 limit ，请你同时删除树中所有 不足节点 ，并返回最终二叉树的根节点。

假如通过节点 node 的每种可能的 “根-叶” 路径上值的总和全都小于给定的 limit，则该节点被称之为 不足节点 ，需要被删除。

叶子节点，就是没有子节点的节点。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 后序遍历删除不足节点。
    /// 叶子节点：若从根到该叶子的路径和 < limit，则删除。
    /// 非叶子节点：递归处理左右子树后，若左右子树都被删除（变为叶子），
    /// 则当前节点也需按叶子规则判断是否删除。
    pub fn sufficient_subset(root: Option<Rc<RefCell<TreeNode>>>, limit: i32) -> Option<Rc<RefCell<TreeNode>>> {
        let Some(node) = root else { return None };

        let mut n = node.borrow_mut();
        let val = n.val;

        // 叶子节点判断
        if n.left.is_none() && n.right.is_none() {
            return if val < limit { None } else { drop(n); Some(node) };
        }

        // 后序处理：先递归左右子树，limit 减去当前节点值
        let left = Self::sufficient_subset(n.left.take(), limit - val);
        let right = Self::sufficient_subset(n.right.take(), limit - val);

        n.left = left;
        n.right = right;

        // 若左右子树都被删光，当前节点变成叶子，需重新判断
        if n.left.is_none() && n.right.is_none() {
            drop(n);
            None
        } else {
            drop(n);
            Some(node)
        }
    }
}
```
