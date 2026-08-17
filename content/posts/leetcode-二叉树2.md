---
title: "leetcode-二叉树2"
date: 2026-08-15T11:32:27+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 对称二叉树

给你一个二叉树的根节点 root ， 检查它是否轴对称。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 检查二叉树是否轴对称
    pub fn is_symmetric(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        match root {
            None => true, // 空树是对称的
            Some(root) => {
                let root = root.borrow();
                Self::mirror_eq(&root.left, &root.right)
            }
        }
    }

    /// 递归判断两棵子树是否互为镜像
    fn mirror_eq(
        left: &Option<Rc<RefCell<TreeNode>>>,
        right: &Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        match (left, right) {
            (None, None) => true, // 两边都空 → 对称
            (Some(l), Some(r)) => {
                let (l, r) = (l.borrow(), r.borrow());
                l.val == r.val
                    && Self::mirror_eq(&l.left, &r.right)  // 左子树的左 vs 右子树的右
                    && Self::mirror_eq(&l.right, &r.left)  // 左子树的右 vs 右子树的左
            }
            _ => false, // 一边空一边非空 → 不对称
        }
    }
}
```
