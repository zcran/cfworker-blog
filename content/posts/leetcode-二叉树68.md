---
title: "leetcode-二叉树68"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 判断根结点是否等于子结点之和

给你一个 二叉树 的根结点 root，该二叉树由恰好 3 个结点组成：根结点、左子结点和右子结点。

如果根结点值等于两个子结点值之和，返回 true ，否则返回 false 。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn check_tree(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        let r = root.unwrap();
        let r = r.borrow();
        let l = r.left.as_ref().unwrap().borrow();
        let rt = r.right.as_ref().unwrap().borrow();
        r.val == l.val + rt.val
    }
}
```
