---
title: "leetcode-二叉树61"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 统计二叉树中好节点的数目

给你一棵根为 root 的二叉树，请你返回二叉树中好节点的数目。

「好节点」X 定义为：从根到该节点 X 所经过的节点中，没有任何节点的值大于 X 的值。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn good_nodes(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(root, i32::MIN)
    }

    /// DFS 遍历，max_sofar 记录从根到当前节点路径上的最大值
    fn dfs(node: Option<Rc<RefCell<TreeNode>>>, max_sofar: i32) -> i32 {
        let Some(n) = node else { return 0 };
        let n = n.borrow();
        let is_good = if n.val >= max_sofar { 1 } else { 0 };
        let new_max = max_sofar.max(n.val);
        is_good + Self::dfs(n.left.clone(), new_max) + Self::dfs(n.right.clone(), new_max)
    }
}
```
