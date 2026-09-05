---
title: "leetcode-二叉树88"
date: 2026-08-15T11:32:33+08:00
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
    /// 返回二叉树最底层最左边节点的值。
    ///
    /// 使用 BFS 层序遍历，记录每层第一个节点的值。
    /// 遍历结束后，最后记录的值即为最底层最左边的节点值。
    pub fn find_bottom_left_value(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut queue = VecDeque::new();
        let mut ans = 0;

        if let Some(node) = root {
            queue.push_back(node);
        }

        while !queue.is_empty() {
            let level_size = queue.len();
            // 每层第一个节点即为该层最左节点
            ans = queue.front().unwrap().borrow().val;

            for _ in 0..level_size {
                let node = queue.pop_front().unwrap();
                let n = node.borrow();

                if let Some(left) = n.left.as_ref() {
                    queue.push_back(left.clone());
                }
                if let Some(right) = n.right.as_ref() {
                    queue.push_back(right.clone());
                }
            }
        }

        ans
    }
}
```
