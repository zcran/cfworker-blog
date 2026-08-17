---
title: "leetcode-二叉树19"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 出现次数最多的子树元素和

给你一个二叉树的根结点 root ，请返回出现次数最多的子树元素和。如果有多个元素出现的次数相同，返回所有出现次数最多的子树元素和（不限顺序）。

一个结点的 「子树元素和」 定义为以该结点为根的二叉树上所有结点的元素之和（包括结点本身）。




```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashMap;

impl Solution {
    /// 返回出现次数最多的子树元素和（子树和 = 以该节点为根的所有节点值之和）
    pub fn find_frequent_tree_sum(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut cnt = HashMap::new();
        Self::dfs(&root, &mut cnt);

        let max_freq = cnt.values().copied().max().unwrap_or(0);
        cnt.into_iter()
            .filter(|(_, v)| *v == max_freq)
            .map(|(k, _)| k)
            .collect()
    }

    /// 后序遍历计算子树和，同时统计每个和的出现次数
    /// 返回当前子树的元素和
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, cnt: &mut HashMap<i32, i32>) -> i32 {
        let node = match node {
            Some(n) => n.borrow(),
            None => return 0,
        };

        let sum = node.val + Self::dfs(&node.left, cnt) + Self::dfs(&node.right, cnt);
        *cnt.entry(sum).or_insert(0) += 1;
        sum
    }
}
```
