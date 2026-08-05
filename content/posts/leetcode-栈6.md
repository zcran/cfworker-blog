---
title: "leetcode-栈6"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 二叉树的后序遍历

给你一棵二叉树的根节点 root ，返回其节点值的 后序遍历 。


```
// 后序遍历：左 -> 右 -> 根
// 使用双栈法：先按"根->右->左"顺序遍历，再反转结果
use std::{
    cell::RefCell,
    rc::Rc,
};

impl Solution {
    pub fn postorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut result = Vec::with_capacity(128);
        let mut stack = Vec::new();
        let mut output = Vec::new(); // 辅助栈，存储逆序结果

        if let Some(node) = root {
            stack.push(node);
        }

        // 先序遍历变体：根 -> 右 -> 左
        while let Some(node) = stack.pop() {
            let node_ref = node.borrow();
            output.push(node_ref.val);

            // 注意：先压左再压右，保证弹出顺序为"根->右->左"
            if let Some(left) = node_ref.left.clone() {
                stack.push(left);
            }
            if let Some(right) = node_ref.right.clone() {
                stack.push(right);
            }
        }

        // 反转得到后序遍历：左 -> 右 -> 根
        while let Some(val) = output.pop() {
            result.push(val);
        }

        result
    }
}
```
