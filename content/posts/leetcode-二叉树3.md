---
title: "leetcode-二叉树3"
date: 2026-08-15T11:32:27+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的层序遍历

给你二叉树的根节点 root ，返回其节点值的 层序遍历 。 （即逐层地，从左到右访问所有节点）。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 二叉树层序遍历：逐层从左到右收集节点值
    pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
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
        ans
    }
}
```
