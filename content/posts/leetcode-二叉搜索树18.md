---
title: "leetcode-二叉搜索树18"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



## 二叉搜索树中的插入操作


给定二叉搜索树（BST）的根节点 root 和要插入树中的值 value ，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据 保证 ，新值和原始二叉搜索树中的任意节点值都不同。

注意，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。 你可以返回 任意有效的结果 。


```
use std::cell::RefCell;
use std::rc::Rc;
impl Solution {
    pub fn insert_into_bst(root: Option<Rc<RefCell<TreeNode>>>,val: i32) -> Option<Rc<RefCell<TreeNode>>> {
        if let Some(r) = &root{{
            let mut root = r.borrow_mut();
            if val < root.val {
                root.left=Self::insert_into_bst(root.left.take(),val)
            } else {
                root.right=Self::insert_into_bst(root.right.take(),val)
            }}
            root
        }else{Some(Rc::new(RefCell::new(TreeNode {left:None,right:None,val: val})))}
    }
}

```
