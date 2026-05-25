---
title: "leetcode-二叉搜索树17"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索树中的搜索

给定二叉搜索树（BST）的根节点 root 和一个整数值 val。

你需要在 BST 中找到节点值等于 val 的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 null 。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::cmp::Ordering;

impl Solution {
    pub fn search_bst(root: Option<Rc<RefCell<TreeNode>>>, val: i32) -> Option<Rc<RefCell<TreeNode>>> {
        let mut current = root;
        while let Some(node) = current {
            let ord = {
                let node_ref = node.borrow();
                node_ref.val.cmp(&val)
            };
            match ord {
                Ordering::Equal => return Some(node),
                Ordering::Less => current = node.borrow().right.clone(),
                Ordering::Greater => current = node.borrow().left.clone(),
            }
        }
        None
    }
}
```
