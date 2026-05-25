---
title: "leetcode-二叉搜索树21"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



## 二叉搜索树的范围和


给定二叉搜索树的根结点 root，返回值位于范围 [low, high] 之间的所有结点的值的和。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn range_sum_bst(root: Option<Rc<RefCell<TreeNode>>>, low: i32, high: i32) -> i32 {
        root.map(|node| {
            let n = node.borrow();
            let v = n.val;
            if v > high {
                Self::range_sum_bst(n.left.clone(), low, high)
            } else if v < low {
                Self::range_sum_bst(n.right.clone(), low, high)
            } else {
                v + Self::range_sum_bst(n.left.clone(), low, high)
                  + Self::range_sum_bst(n.right.clone(), low, high)
            }
        }).unwrap_or(0)  // 若 root 为 None 则返回 0
    }
}
```
