---
title: "leetcode-二叉树90"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 彩灯装饰记录 I

一棵圣诞树记作根节点为 root 的二叉树，节点值为该位置装饰彩灯的颜色编号。请按照从 左 到 右 的顺序返回每一层彩灯编号。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 按从左到右的顺序返回二叉树每层彩灯编号（层序遍历）。
    ///
    /// 使用 BFS 队列，父节点出队时将其值加入结果，并将左右子节点入队。
    pub fn decorate_record(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();

        if let Some(node) = root {
            queue.push_back(node);
        }

        while let Some(node) = queue.pop_front() {
            let n = node.borrow();
            ans.push(n.val);

            if let Some(left) = n.left.as_ref() {
                queue.push_back(left.clone());
            }
            if let Some(right) = n.right.as_ref() {
                queue.push_back(right.clone());
            }
        }

        ans
    }
}
```
