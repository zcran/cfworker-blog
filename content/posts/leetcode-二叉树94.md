---
title: "leetcode-二叉树90"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 彩灯装饰记录 II

一棵圣诞树记作根节点为 root 的二叉树，节点值为该位置装饰彩灯的颜色编号。请按照从左到右的顺序返回每一层彩灯编号，每一层的结果记录于一行。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 按层从左到右返回彩灯编号，每一层的结果记录于一行。
    ///
    /// 使用 BFS 层序遍历，通过队列长度控制分层。
    pub fn decorate_record(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();

        if let Some(node) = root {
            queue.push_back(node);
        }

        while !queue.is_empty() {
            let level_size = queue.len();
            let mut level = Vec::with_capacity(level_size);

            for _ in 0..level_size {
                let node = queue.pop_front().unwrap();
                let n = node.borrow();
                level.push(n.val);

                if let Some(left) = n.left.as_ref() {
                    queue.push_back(left.clone());
                }
                if let Some(right) = n.right.as_ref() {
                    queue.push_back(right.clone());
                }
            }

            ans.push(level);
        }

        ans
    }
}
```
