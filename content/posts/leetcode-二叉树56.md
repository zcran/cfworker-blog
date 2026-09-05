---
title: "leetcode-二叉树56"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 祖父节点值为偶数的节点和

给你一棵二叉树，请你返回满足以下条件的所有节点的值之和：

该节点的祖父节点的值为偶数。（一个节点的祖父节点是指该节点的父节点的父节点。）

如果不存在祖父节点值为偶数的节点，那么返回 0 。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// DFS 遍历，维护父节点和祖父节点是否为偶数的状态。
    /// 若祖父节点为偶数，则将当前节点值累加至总和。
    pub fn sum_even_grandparent(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(&root, false, false)
    }

    fn dfs(
        node: &Option<Rc<RefCell<TreeNode>>>,
        parent_even: bool,
        grandparent_even: bool,
    ) -> i32 {
        let Some(n) = node else { return 0 };
        let n = n.borrow();

        let mut sum = if grandparent_even { n.val } else { 0 };
        let is_even = n.val % 2 == 0;

        sum += Self::dfs(&n.left, is_even, parent_even);
        sum += Self::dfs(&n.right, is_even, parent_even);
        sum
    }
}
```
