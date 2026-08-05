---
title: "leetcode-链表7"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 二叉树展开为链表


给你二叉树的根结点 root ，请你将它展开为一个单链表：

展开后的单链表应该同样使用 TreeNode ，其中 right 子指针指向链表中下一个结点，而左子指针始终为 null 。

展开后的单链表应该与二叉树 先序遍历 顺序相同。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, head: &mut Option<Rc<RefCell<TreeNode>>>) {
        if let Some(x) = node {
            let mut x = x.borrow_mut();
            // 右 - 左 - 根
            Self::dfs(&x.right, head);
            Self::dfs(&x.left, head);
            x.left = None;
            x.right = head.take(); // 头插法，相当于链表的 node.next = head
            *head = node.clone(); // 现在链表头节点是 node
        }
    }

    pub fn flatten(root: &mut Option<Rc<RefCell<TreeNode>>>) { // mut 可以去掉
        let mut head = None;
        Self::dfs(&root, &mut head);
    }
}
```
