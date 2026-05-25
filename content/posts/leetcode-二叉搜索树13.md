---
title: "leetcode-二叉搜索树13"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



## 二叉搜索树的最小绝对差



给你一个二叉搜索树的根节点 root ，返回 树中任意两不同节点值之间的最小差值 。

差值是一个正数，其数值等于两值之差的绝对值。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn get_minimum_difference(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut min_diff = i32::MAX;
        let mut prev_val = None;
        let mut stack = Vec::new();
        let mut current = root;

        // 迭代中序遍历
        while !stack.is_empty() || current.is_some() {
            // 走到最左节点
            while let Some(node) = current {
                stack.push(node.clone());
                current = node.borrow().left.clone();
            }
            // 弹出栈顶节点
            if let Some(node) = stack.pop() {
                let val = node.borrow().val;
                // 计算与前一个节点的差值
                if let Some(prev) = prev_val {
                    min_diff = min_diff.min(val - prev);
                }
                prev_val = Some(val);
                // 转向右子树
                current = node.borrow().right.clone();
            }
        }
        min_diff
    }
}
```
