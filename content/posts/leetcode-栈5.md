---
title: "leetcode-栈5"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 二叉树的前序遍历

给你二叉树的根节点 root ，返回它节点值的 前序 遍历。


```
// 前序遍历：根 -> 左 -> 右
// 使用栈模拟递归，每次弹出节点后先压右子节点再压左子节点
use std::{
    cell::RefCell,
    rc::Rc,
};

impl Solution {
    pub fn preorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut result = Vec::with_capacity(128); // 预分配减少扩容
        let mut stack = Vec::new();

        if let Some(root_node) = root {
            stack.push(root_node);
        }

        while let Some(node) = stack.pop() {
            let node_ref = node.borrow();
            result.push(node_ref.val);

            // 先压右子节点，再压左子节点，保证左子节点先被处理
            if let Some(right) = node_ref.right.clone() {
                stack.push(right);
            }
            if let Some(left) = node_ref.left.clone() {
                stack.push(left);
            }
        }

        result
    }
}
```
