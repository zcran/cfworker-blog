---
title: "leetcode-二叉树32"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 最长同值路径

给定一个二叉树的 root ，返回 最长的路径的长度 ，这个路径中的 每个节点具有相同值 。 这条路径可以经过也可以不经过根节点。

两个节点之间的路径长度 由它们之间的边数表示。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回最长同值路径的长度（边数）
    ///
    /// 后序遍历，对每个节点计算左右同值链长度，
    /// 经过该节点的最长路径 = left + right，向上返回 max(left, right) + 1。
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn longest_univalue_path(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut ans = 0;
        Self::dfs(root, &mut ans);
        ans
    }

    /// 返回从该节点向下延伸的最长同值链长度（边数）
    fn dfs(node: Option<Rc<RefCell<TreeNode>>>, ans: &mut i32) -> i32 {
        let Some(node) = node else { return 0 };

        let n = node.borrow();
        let left = Self::dfs(n.left.clone(), ans);
        let right = Self::dfs(n.right.clone(), ans);

        let mut left_len = 0;
        let mut right_len = 0;

        if let Some(ref left_node) = n.left {
            if left_node.borrow().val == n.val {
                left_len = left + 1;
            }
        }
        if let Some(ref right_node) = n.right {
            if right_node.borrow().val == n.val {
                right_len = right + 1;
            }
        }

        *ans = (*ans).max(left_len + right_len);
        left_len.max(right_len)
    }
}
```
