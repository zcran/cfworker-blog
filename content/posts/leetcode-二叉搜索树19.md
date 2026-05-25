---
title: "leetcode-二叉搜索树19"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



## 二叉搜索树节点最小距离


给你一个二叉搜索树的根节点 root ，返回 树中任意两不同节点值之间的最小差值 。

差值是一个正数，其数值等于两值之差的绝对值。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn min_diff_in_bst(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        // 中序遍历生成升序序列（纯函数递归）
        fn inorder_seq(node: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
            match node {
                None => vec![],
                Some(n) => {
                    let n_ref = n.borrow();
                    let mut left = inorder_seq(n_ref.left.clone());
                    let mut right = inorder_seq(n_ref.right.clone());
                    left.push(n_ref.val);
                    left.append(&mut right);
                    left
                }
            }
        }
        let seq = inorder_seq(root);
        // 扫描相邻差值，取最小值
        seq.windows(2)
            .map(|w| w[1] - w[0])
            .min()
            .unwrap_or(0) // 题目保证至少有两个节点，但安全起见返回 0
    }
}
```
