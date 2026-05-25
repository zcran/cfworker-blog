---
title: "leetcode-二叉搜索树22"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 前序遍历构造二叉搜索树


给定一个整数数组，它表示BST(即 二叉搜索树 )的 先序遍历 ，构造树并返回其根。

保证 对于给定的测试用例，总是有可能找到具有给定需求的二叉搜索树。

二叉搜索树 是一棵二叉树，其中每个节点， Node.left 的任何后代的值 严格小于 Node.val , Node.right 的任何后代的值 严格大于 Node.val。

二叉树的 前序遍历 首先显示节点的值，然后遍历Node.left，最后遍历Node.right。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn bst_from_preorder(preorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        Self::build(&preorder)
    }

    fn build(slice: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
        slice.split_first().map(|(&val, rest)| {
            let split_idx = rest.iter().position(|&x| x > val).unwrap_or(rest.len());
            Rc::new(RefCell::new(TreeNode {
                val,
                left: Self::build(&rest[..split_idx]),
                right: Self::build(&rest[split_idx..]),
            }))
        })
    }
}
```
