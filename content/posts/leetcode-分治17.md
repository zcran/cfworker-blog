---
title: "leetcode-分治17"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 根据前序和后序遍历构造二叉树


给定两个整数数组，preorder 和 postorder ，其中 preorder 是一个具有 无重复 值的二叉树的前序遍历，postorder 是同一棵树的后序遍历，重构并返回二叉树。

如果存在多个答案，您可以返回其中 任何 一个。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn construct_from_pre_post(preorder: Vec<i32>, postorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        if preorder.is_empty() { // 空节点
            return None;
        }
        if preorder.len() == 1 { // 叶子节点
            return Some(Rc::new(RefCell::new(TreeNode::new(preorder[0]))));
        }
        let left_size = postorder.iter().position(|&x| x == preorder[1]).unwrap() + 1; // 左子树的大小
        let pre1 = preorder[1..1 + left_size].to_vec();
        let pre2 = preorder[1 + left_size..].to_vec();
        let post1 = postorder[..left_size].to_vec();
        let post2 = postorder[left_size..postorder.len() - 1].to_vec();
        let left = Self::construct_from_pre_post(pre1, post1);
        let right = Self::construct_from_pre_post(pre2, post2);
        Some(Rc::new(RefCell::new(TreeNode { val: preorder[0], left, right })))
    }
}

```
