---
title: "leetcode-二叉树1"
date: 2026-08-15T11:32:27+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 相同的树

给你两棵二叉树的根节点 p 和 q ，编写一个函数来检验这两棵树是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 判断两棵二叉树是否相同（结构和节点值均相同）
    pub fn is_same_tree(
        p: Option<Rc<RefCell<TreeNode>>>,
        q: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        match (p, q) {
            (None, None) => true, // 两棵空树相同
            (Some(p), Some(q)) => {
                let (p, q) = (p.borrow(), q.borrow());
                p.val == q.val
                    && Self::is_same_tree(p.left.clone(), q.left.clone())
                    && Self::is_same_tree(p.right.clone(), q.right.clone())
            }
            _ => false, // 一棵空、一棵非空 → 不同
        }
    }
}
```
