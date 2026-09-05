---
title: "leetcode-二叉树59"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树中的最长交错路径

给你一棵以 root 为根的二叉树，二叉树中的交错路径定义如下：

选择二叉树中 任意 节点和一个方向（左或者右）。
如果前进方向为右，那么移动到当前节点的的右子节点，否则移动到它的左子节点。
改变前进方向：左变右或者右变左。
重复第二步和第三步，直到你在树中无法继续移动。

交错路径的长度定义为：访问过的节点数目 - 1（单个节点的路径长度为 0 ）。

请你返回给定树中最长 交错路径 的长度。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 后序遍历。每个节点返回 (left_len, right_len)：
    /// - left_len: 从当前节点出发，第一步向左的最长交错路径长度
    /// - right_len: 从当前节点出发，第一步向右的最长交错路径长度
    ///
    /// 若左子节点存在，left_len = 1 + 左子节点的 right_len（走到左子节点后下一步必须向右）；
    /// 若右子节点存在，right_len = 1 + 右子节点的 left_len。
    pub fn longest_zig_zag(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut ans = 0;
        Self::dfs(&root, &mut ans);
        ans
    }

    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, ans: &mut i32) -> (i32, i32) {
        let Some(n) = node else { return (0, 0) };
        let n = n.borrow();

        let (ll, lr) = Self::dfs(&n.left, ans);
        let (rl, rr) = Self::dfs(&n.right, ans);

        let left = if n.left.is_some() { 1 + lr } else { 0 };
        let right = if n.right.is_some() { 1 + rl } else { 0 };

        *ans = (*ans).max(left).max(right);
        (left, right)
    }
}
```
