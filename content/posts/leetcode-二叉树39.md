---
title: "leetcode-二叉树39"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 单值二叉树

如果二叉树每个节点都具有相同的值，那么该二叉树就是单值二叉树。

只有给定的树是单值二叉树时，才返回 true；否则返回 false。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 判断二叉树是否为单值树（所有节点值相同）
    ///
    /// 先取根节点值作为基准，递归检查每个节点是否等于该值。
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn is_unival_tree(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        let val = match &root {
            Some(node) => node.borrow().val,
            None => return true,
        };
        Self::check(root, val)
    }

    fn check(node: Option<Rc<RefCell<TreeNode>>>, val: i32) -> bool {
        let Some(node) = node else { return true };

        let n = node.borrow();
        n.val == val
            && Self::check(n.left.clone(), val)
            && Self::check(n.right.clone(), val)
    }
}
```
