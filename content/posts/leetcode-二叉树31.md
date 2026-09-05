---
title: "leetcode-二叉树31"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树中第二小的节点

给定一个非空特殊的二叉树，每个节点都是正数，并且每个节点的子节点数量只能为 2 或 0。如果一个节点有两个子节点的话，那么该节点的值等于两个子节点中较小的一个。

更正式地说，即 root.val = min(root.left.val, root.right.val) 总成立。

给出这样的一个二叉树，你需要输出所有节点中的 第二小的值 。

如果第二小的值不存在的话，输出 -1 。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回特殊二叉树中的第二小值
    ///
    /// 性质：每个节点值等于两子节点较小值，故根节点为全局最小值。
    /// 只需找大于根节点的最小值，递归遍历，剪枝跳过等于根值的子树。
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn find_second_minimum_value(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let root = match root {
            Some(r) => r,
            None => return -1,
        };

        let min_val = root.borrow().val;
        Self::dfs(root, min_val)
    }

    fn dfs(node: Rc<RefCell<TreeNode>>, min_val: i32) -> i32 {
        let n = node.borrow();

        // 当前节点值大于 min_val，由于子树所有值 >= 当前值，直接返回
        if n.val > min_val {
            return n.val;
        }

        // 叶子节点且值等于 min_val，无第二小值
        let (left, right) = match (n.left.clone(), n.right.clone()) {
            (Some(l), Some(r)) => (l, r),
            _ => return -1,
        };

        // 递归搜索两子树
        let left_ans = Self::dfs(left, min_val);
        let right_ans = Self::dfs(right, min_val);

        match (left_ans, right_ans) {
            (-1, -1) => -1,
            (-1, r) => r,
            (l, -1) => l,
            (l, r) => l.min(r),
        }
    }
}
```
