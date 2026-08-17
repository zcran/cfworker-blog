---
title: "leetcode-二叉树4"
date: 2026-08-15T11:32:27+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的锯齿形层序遍历

给你二叉树的根节点 root ，返回其节点值的 锯齿形层序遍历 。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 锯齿形层序遍历：奇数层从左到右，偶数层从右到左
    pub fn zigzag_level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut ans = vec![];
        let mut q = VecDeque::new();

        if let Some(root) = root {
            q.push_back(root);
        }

        let mut left_to_right = true;
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

            if !left_to_right {
                vals.reverse();
            }
            ans.push(vals);
            left_to_right = !left_to_right;
        }

        ans
    }
}
```
