---
title: "leetcode-二叉树55"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 层数最深叶子节点的和

给你一棵二叉树的根节点 root ，请你返回 层数最深的叶子节点的和 。


```
use std::cell::RefCell;
use std::collections::VecDeque;
use std::rc::Rc;

impl Solution {
    /// BFS 层序遍历。每层累加节点值，遍历完最后一层时，
    /// ret 即为层数最深的叶子节点的和。
    pub fn deepest_leaves_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let Some(root) = root else { return 0 };
        let mut queue = VecDeque::new();
        queue.push_back(root);
        let mut ret = 0;

        while !queue.is_empty() {
            ret = 0;
            // 固定当前层节点数，防止动态入队干扰循环
            for _ in 0..queue.len() {
                let node = queue.pop_front().unwrap();
                let n = node.borrow();
                ret += n.val;

                if let Some(left) = n.left.as_ref() {
                    queue.push_back(left.clone());
                }
                if let Some(right) = n.right.as_ref() {
                    queue.push_back(right.clone());
                }
            }
        }

        ret
    }
}
```
