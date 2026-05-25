---
title: "leetcode-二叉搜索树25"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索子树的最大键值和

给你一棵以 root 为根的 二叉树 ，请你返回 任意 二叉搜索子树的最大键值和。

二叉搜索树的定义如下：

任意节点的左子树中的键值都 小于 此节点的键值。
任意节点的右子树中的键值都 大于 此节点的键值。
任意节点的左子树和右子树都是二叉搜索树。


```
use std::rc::Rc;
use std::cell::RefCell;

#[derive(Debug)]
struct SubtreeInfo {
    is_bst: bool,
    min: i32,
    max: i32,
    sum: i32,        // 当前子树作为BST时的和（仅当 is_bst 为 true 时有效）
    max_sum: i32,    // 当前子树中所有 BST 的最大和
}

impl Solution {
    pub fn max_sum_bst(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        fn dfs(node: &Option<Rc<RefCell<TreeNode>>>) -> SubtreeInfo {
            match node {
                None => SubtreeInfo {
                    is_bst: true,
                    min: i32::MAX,
                    max: i32::MIN,
                    sum: 0,
                    max_sum: 0,
                },
                Some(rc) => {
                    let n = rc.borrow();
                    let left = dfs(&n.left);
                    let right = dfs(&n.right);

                    let is_bst = left.is_bst
                        && right.is_bst
                        && n.val > left.max
                        && n.val < right.min;

                    if is_bst {
                        let sum = left.sum + right.sum + n.val;
                        let min_val = left.min.min(n.val);
                        let max_val = right.max.max(n.val);
                        let max_sum = sum.max(left.max_sum).max(right.max_sum);
                        SubtreeInfo {
                            is_bst: true,
                            min: min_val,
                            max: max_val,
                            sum,
                            max_sum,
                        }
                    } else {
                        let max_sum = left.max_sum.max(right.max_sum);
                        SubtreeInfo {
                            is_bst: false,
                            min: 0,
                            max: 0,
                            sum: 0,
                            max_sum,
                        }
                    }
                }
            }
        }
        dfs(&root).max_sum
    }
}
```
