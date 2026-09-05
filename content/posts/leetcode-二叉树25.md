---
title: "leetcode-二叉树25"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 合并二叉树

给你两棵二叉树： root1 和 root2 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，不为 null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

注意: 合并过程必须从两个树的根节点开始。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 合并两棵二叉树
    ///
    /// 重叠节点值相加，不重叠的直接保留原树节点。不修改输入树。
    pub fn merge_trees(
        root1: Option<Rc<RefCell<TreeNode>>>,
        root2: Option<Rc<RefCell<TreeNode>>>,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        match (root1, root2) {
            (Some(n1), Some(n2)) => {
                let (v1, l1, r1) = {
                    let b = n1.borrow();
                    (b.val, b.left.clone(), b.right.clone())
                };
                let (v2, l2, r2) = {
                    let b = n2.borrow();
                    (b.val, b.left.clone(), b.right.clone())
                };

                Some(Rc::new(RefCell::new(TreeNode {
                    val: v1 + v2,
                    left: Self::merge_trees(l1, l2),
                    right: Self::merge_trees(r1, r2),
                })))
            }
            (None, r) => r,
            (l, None) => l,
        }
    }
}
```
