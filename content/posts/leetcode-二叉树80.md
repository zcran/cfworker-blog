---
title: "leetcode-二叉树80"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 统计二叉树中支配节点的数量

给你一棵 完全二叉树 的根节点 root。

如果节点 x 的值等于以 x 为根的子树中所有节点值的 最大值，则称节点 x 为 支配节点 。

返回给定树中 支配节点 的数量。

完全二叉树 是指除最后一层外，其余各层都被完全填满，并且最后一层的所有节点都尽可能靠左排列的二叉树。

树中以节点 x 为根的 子树 由节点 x 及其所有后代节点组成。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn count_dominant_nodes(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut ans = 0;
        Self::dfs(&root, &mut ans);
        ans
    }

    /// 返回以 node 为根的子树中的最大值
    /// 同时统计支配节点数量到 ans
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, ans: &mut i32) -> i32 {
        let Some(n) = node else { return i32::MIN };
        let n = n.borrow();

        let left_max = Self::dfs(&n.left, ans);
        let right_max = Self::dfs(&n.right, ans);
        let subtree_max = left_max.max(right_max);

        if n.val >= subtree_max {
            *ans += 1;
        }

        n.val.max(subtree_max)
    }
}
```
