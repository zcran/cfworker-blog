---
title: "leetcode-二叉树18"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 路径总和 III

给定一个二叉树的根节点 root ，和一个整数 targetSum ，求该二叉树里节点值之和等于 targetSum 的 路径 的数目。

路径 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashMap;

impl Solution {
    /// 返回节点值之和等于 target_sum 的向下路径数目
    /// 路径不需要从根开始，也不需要在叶子结束
    pub fn path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> i32 {
        let mut cnt = HashMap::new();
        cnt.insert(0, 1); // 前缀和为 0 出现 1 次（空路径）
        let mut ans = 0;
        Self::dfs(&root, 0, target_sum as i64, &mut ans, &mut cnt);
        ans
    }

    /// s: 从根到当前节点父节点的前缀和（不含当前节点）
    fn dfs(
        node: &Option<Rc<RefCell<TreeNode>>>,
        s: i64,
        target: i64,
        ans: &mut i32,
        cnt: &mut HashMap<i64, i32>,
    ) {
        let node = match node {
            Some(n) => n.borrow(),
            None => return,
        };

        let s = s + node.val as i64;

        // 以当前节点为终点的路径数 = 前缀和为 (s - target) 的出现次数
        *ans += cnt.get(&(s - target)).copied().unwrap_or(0);

        // 当前前缀和入表，递归子树，然后恢复现场
        *cnt.entry(s).or_insert(0) += 1;
        Self::dfs(&node.left, s, target, ans, cnt);
        Self::dfs(&node.right, s, target, ans, cnt);
        *cnt.entry(s).or_insert(0) -= 1;
    }
}
```
