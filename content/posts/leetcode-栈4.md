---
title: "leetcode-栈4"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 二叉树的中序遍历

给定一个二叉树的根节点 root ，返回 它的 中序 遍历 。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn inorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut result = Vec::with_capacity(64); // 预分配容量，减少扩容
        let mut stack = Vec::with_capacity(64);
        let mut current = root;

        // 迭代中序遍历：左 -> 根 -> 右
        while current.is_some() || !stack.is_empty() {
            // 一直向左，将路径上的节点入栈
            while let Some(node) = current {
                stack.push(node.clone());
                current = node.borrow().left.clone();
            }

            // 访问栈顶节点（最左节点）
            if let Some(node) = stack.pop() {
                let node_ref = node.borrow();
                result.push(node_ref.val);
                current = node_ref.right.clone();
            }
        }

        result
    }
}
```
