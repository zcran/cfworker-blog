---
title: "leetcode-二叉树20"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 找树左下角的值

给定一个二叉树的 根节点 root，请找出该二叉树的 最底层 最左边 节点的值。

假设二叉树中至少有一个节点。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 返回二叉树最底层最左边节点的值（BFS 层序遍历，每层第一个即最左）
    pub fn find_bottom_left_value(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut q = VecDeque::new();
        q.push_back(root.unwrap());

        let mut ans = 0;
        while !q.is_empty() {
            let level_size = q.len();
            for i in 0..level_size {
                let node = q.pop_front().unwrap();
                let node = node.borrow();
                // 每层第一个节点 → 当前层最左
                if i == 0 {
                    ans = node.val;
                }
                if let Some(left) = node.left.clone() {
                    q.push_back(left);
                }
                if let Some(right) = node.right.clone() {
                    q.push_back(right);
                }
            }
        }
        ans
    }
}
```
