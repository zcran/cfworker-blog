---
title: "leetcode-二叉树15"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的最近公共祖先

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉树中两个节点 p 和 q 的最近公共祖先（LCA）
    pub fn lowest_common_ancestor(
        root: Option<Rc<RefCell<TreeNode>>>,
        p: Option<Rc<RefCell<TreeNode>>>,
        q: Option<Rc<RefCell<TreeNode>>>,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        // 空树，或当前节点就是 p/q 之一 → 直接返回
        if root.is_none() || root == p || root == q {
            return root;
        }

        let node = root.as_ref()?;
        let left = Self::lowest_common_ancestor(node.borrow().left.clone(), p.clone(), q.clone());
        let right = Self::lowest_common_ancestor(node.borrow().right.clone(), p, q);

        // 左右子树各找到一个 → 当前节点即为 LCA
        match (left, right) {
            (Some(_), Some(_)) => root,
            (Some(l), None) => Some(l),
            (None, Some(r)) => Some(r),
            (None, None) => None,
        }
    }
}
```
