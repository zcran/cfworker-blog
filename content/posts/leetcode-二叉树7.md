---
title: "leetcode-二叉树7"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 平衡二叉树

给定一个二叉树，判断它是否是 平衡二叉树


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 判断二叉树是否平衡：任意节点的左右子树高度差不超过 1
    pub fn is_balanced(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        match root {
            Some(node) => {
                let l = Self::height(node.borrow_mut().left.take());
                let r = Self::height(node.borrow_mut().right.take());
                l != -1 && r != -1 && (l - r).abs() <= 1
            }
            None => true,
        }
    }

    /// 返回树的高度；若不平衡则返回 -1（剪枝标记）
    fn height(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        match root {
            Some(node) => {
                let l = Self::height(node.borrow_mut().left.take());
                // 左子树已不平衡，直接剪枝，无需遍历右子树
                if l == -1 {
                    return -1;
                }
                let r = Self::height(node.borrow_mut().right.take());
                if r == -1 || (l - r).abs() > 1 {
                    -1
                } else {
                    l.max(r) + 1
                }
            }
            None => 0,
        }
    }
}
```
