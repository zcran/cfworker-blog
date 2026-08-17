---
title: "leetcode-二叉树6"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的层序遍历 II

给你二叉树的根节点 root ，返回其节点值 自底向上的层序遍历 。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 自底向上的层序遍历：从叶子层到根节点层，每层从左到右
    pub fn level_order_bottom(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut ans = vec![];
        let mut q = VecDeque::new();

        if let Some(root) = root {
            q.push_back(root);
        }

        while !q.is_empty() {
            let level_size = q.len();
            let mut vals = Vec::with_capacity(level_size);

            for _ in 0..level_size {
                let node = q.pop_front().unwrap();
                let node = node.borrow();
                vals.push(node.val);

                if let Some(left) = node.left.clone() {
                    q.push_back(left);
                }
                if let Some(right) = node.right.clone() {
                    q.push_back(right);
                }
            }
            ans.push(vals);
        }

        ans.reverse();
        ans
    }
}
```
