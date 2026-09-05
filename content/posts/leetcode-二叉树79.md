---
title: "leetcode-二叉树79"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 第 K 大的完美二叉子树的大小

给你一棵 二叉树 的根节点 root 和一个整数k。

返回第 k 大的 完美二叉子树的大小，如果不存在则返回 -1。

完美二叉树 是指所有叶子节点都在同一层级的树，且每个父节点恰有两个子节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn kth_largest_perfect_subtree(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i32 {
        let mut sizes = Vec::new();
        Self::dfs(&root, &mut sizes);

        if sizes.len() < k as usize {
            return -1;
        }

        // 使用 select_nth_unstable 找第 k 大，平均 O(n)
        let idx = sizes.len() - k as usize;
        let (_, &mut ans, _) = sizes.select_nth_unstable(idx);
        ans
    }

    /// 返回 (是否为完美二叉树, 完美子树的大小)
    /// 空节点视为完美二叉树，大小为 0
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, sizes: &mut Vec<i32>) -> (bool, i32) {
        let Some(n) = node else { return (true, 0) };
        let n = n.borrow();

        let (lp, ls) = Self::dfs(&n.left, sizes);
        let (rp, rs) = Self::dfs(&n.right, sizes);

        // 当前子树为完美二叉树：左右都是完美且大小相等
        if lp && rp && ls == rs {
            let size = ls * 2 + 1;
            sizes.push(size);
            (true, size)
        } else {
            (false, 0)
        }
    }
}
```
