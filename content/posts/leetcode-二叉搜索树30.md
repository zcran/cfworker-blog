---
title: "leetcode-二叉搜索树30"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 递增顺序搜索树

给你一棵二叉搜索树，请 按中序遍历 将其重新排列为一棵递增顺序搜索树，使树中最左边的节点成为树的根节点，并且每个节点没有左子节点，只有一个右子节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 纯函数式：返回一棵新的树，其中每个节点只有右孩子，且节点值递增。
    /// 原树保持不变。
    pub fn increasing_bst(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        // 中序遍历收集所有值
        let vals = {
            fn inorder(node: &Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
                match node {
                    None => Vec::new(),
                    Some(rc) => {
                        let n = rc.borrow();
                        let mut left = inorder(&n.left);
                        let mut right = inorder(&n.right);
                        left.push(n.val);
                        left.extend(right);
                        left
                    }
                }
            }
            inorder(&root)
        };

        // 根据值列表构建只有右孩子的树
        fn build_right_only(vals: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
            vals.split_first().map(|(&val, rest)| {
                let mut node = TreeNode::new(val);
                node.right = build_right_only(rest);
                Rc::new(RefCell::new(node))
            })
        }

        build_right_only(&vals)
    }
}
```
