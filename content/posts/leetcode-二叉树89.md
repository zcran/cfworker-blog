---
title: "leetcode-二叉树89"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的右视图

给定一个二叉树的 根节点 root，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 返回二叉树的右视图：从右侧能看到的节点值（每层最右节点）。
    ///
    /// 使用 BFS 层序遍历，取每层最后一个节点的值。
    pub fn right_side_view(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();

        if let Some(node) = root {
            queue.push_back(node);
        }

        while !queue.is_empty() {
            let level_size = queue.len();

            for i in 0..level_size {
                let node = queue.pop_front().unwrap();
                let n = node.borrow();

                // 该层最后一个节点即为最右可见节点
                if i == level_size - 1 {
                    ans.push(n.val);
                }

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
