---
title: "leetcode-二叉树38"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的完全性检验

给你一棵二叉树的根节点 root ，请你判断这棵树是否是一棵 完全二叉树 。

在一棵 完全二叉树 中，除了最后一层外，所有层都被完全填满，并且最后一层中的所有节点都尽可能靠左。最后一层（第 h 层）中可以包含 1 到 2^h 个节点。


```
use std::cell::RefCell;
use std::collections::VecDeque;
use std::rc::Rc;

impl Solution {
    /// 判断二叉树是否为完全二叉树
    ///
    /// BFS 层序遍历，遇到第一个空节点后，后续必须全为空。
    /// 时间复杂度 O(n)，空间复杂度 O(w)，w 为最大宽度
    pub fn is_complete_tree(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        let mut queue = VecDeque::new();
        queue.push_back(root);

        let mut found_null = false;

        while let Some(node) = queue.pop_front() {
            let Some(node) = node else {
                found_null = true;
                continue;
            };

            if found_null {
                return false;
            }

            let n = node.borrow();
            queue.push_back(n.left.clone());
            queue.push_back(n.right.clone());
        }

        true
    }
}
```
