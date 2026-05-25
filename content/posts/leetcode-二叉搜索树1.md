---
title: "leetcode-二叉搜索树1"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 不同的二叉搜索树 II

给你一个整数 n ，请你生成并返回所有由 n 个节点组成且节点值从 1 到 n 互不相同的不同 二叉搜索树 。可以按 任意顺序 返回答案。

```
// 定义树节点结构（题目已给出，此处仅作注释说明）
// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//     pub val: i32,
//     pub left: Option<Rc<RefCell<TreeNode>>>,
//     pub right: Option<Rc<RefCell<TreeNode>>>,
// }
// impl TreeNode {
//     #[inline]
//     pub fn new(val: i32) -> Self { ... }
// }

use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 生成所有由 1..=n 构成的不同的二叉搜索树
    ///
    /// # 参数
    /// - `n`: 节点值的最大值（节点值为 1..=n）
    ///
    /// # 返回
    /// - 包含所有 BST 根节点的向量，每个节点以 `Rc<RefCell<TreeNode>>` 形式共享所有权
    pub fn generate_trees(n: i32) -> Vec<Option<Rc<RefCell<TreeNode>>>> {
        // 定义递归闭包，生成区间 [l, r] 内所有可能的 BST
        fn dfs(l: i32, r: i32) -> Vec<Option<Rc<RefCell<TreeNode>>>> {
            // 空区间：返回包含一个 None 的向量，表示空树
            if l > r {
                return vec![None];
            }
            // 单节点区间：直接返回该节点
            if l == r {
                return vec![Some(Rc::new(RefCell::new(TreeNode::new(l))))];
            }

            // 对于当前区间 [l, r]，枚举每个可能的根节点值 i
            (l..=r)
                .flat_map(|i| {
                    // 递归生成左子树（所有可能结构）和右子树（所有可能结构）
                    let left_trees = dfs(l, i - 1);
                    let right_trees = dfs(i + 1, r);
                    // 将左右子树的所有组合与当前根节点拼接，形成完整的树
                    left_trees
                        .iter()
                        .flat_map(|left| {
                            right_trees
                                .iter()
                                .map(move |right| {
                                    Some(Rc::new(RefCell::new(TreeNode {
                                        val: i,
                                        left: left.clone(),   // Rc::clone 仅增加引用计数
                                        right: right.clone(),
                                    })))
                                })
                                .collect::<Vec<_>>()
                        })
                        .collect::<Vec<_>>()
                })
                .collect()  // 将 flat_map 产生的多个 Vec 展平为一个 Vec
        }

        dfs(1, n)
    }
}
```
