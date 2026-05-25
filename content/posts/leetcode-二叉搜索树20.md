---
title: "leetcode-二叉搜索树20"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 递增顺序搜索树


给你一棵二叉搜索树的 root ，请你 按中序遍历 将其重新排列为一棵递增顺序搜索树，使树中最左边的节点成为树的根节点，并且每个节点没有左子节点，只有一个右子节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn increasing_bst(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        // 1. 中序遍历收集所有节点值（升序）
        fn inorder_collect(node: Option<Rc<RefCell<TreeNode>>>, out: &mut Vec<i32>) {
            if let Some(n) = node {
                let n_ref = n.borrow();
                inorder_collect(n_ref.left.clone(), out);
                out.push(n_ref.val);
                inorder_collect(n_ref.right.clone(), out);
            }
        }
        let mut values = Vec::new();
        inorder_collect(root, &mut values);

        // 2. 从右向左构建链表，每次新建节点
        let mut head = None;
        for &val in values.iter().rev() {
            let mut new_node = TreeNode::new(val);
            new_node.right = head;
            head = Some(Rc::new(RefCell::new(new_node)));
        }
        head
    }
}
```
