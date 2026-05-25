---
title: "leetcode-二叉搜索树26"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 将二叉搜索树变平衡


给你一棵二叉搜索树，请你返回一棵 平衡后 的二叉搜索树，新生成的树应该与原来的树有着相同的节点值。如果有多种构造方法，请你返回任意一种。

如果一棵二叉搜索树中，每个节点的两棵子树高度差不超过 1 ，我们就称这棵二叉搜索树是 平衡的 。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn balance_bst(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        // 收集有序序列
        let mut seq = Vec::new();
        Self::inorder_collect(&root, &mut seq);

        // 定义递归构建函数
        fn build(seq: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
            if seq.is_empty() {
                None
            } else {
                let mid = seq.len() / 2;
                let mut node = TreeNode::new(seq[mid]);
                node.left = build(&seq[..mid]);
                node.right = build(&seq[mid + 1..]);
                Some(Rc::new(RefCell::new(node)))
            }
        }

        build(&seq)
    }

    // 中序遍历（辅助函数）
    fn inorder_collect(node: &Option<Rc<RefCell<TreeNode>>>, out: &mut Vec<i32>) {
        if let Some(rc) = node {
            let n = rc.borrow();
            Self::inorder_collect(&n.left, out);
            out.push(n.val);
            Self::inorder_collect(&n.right, out);
        }
    }
}
```
