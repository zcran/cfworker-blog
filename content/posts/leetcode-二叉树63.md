---
title: "leetcode-二叉树63"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 好叶子节点对的数量

给你二叉树的根节点 root 和一个整数 distance 。

如果二叉树中两个 叶 节点之间的 最短路径长度 小于或者等于 distance ，那它们就可以构成一组 好叶子节点对 。

返回树中 好叶子节点对的数量 。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn count_pairs(root: Option<Rc<RefCell<TreeNode>>>, distance: i32) -> i32 {
        Self::dfs(&root, distance).1
    }

    /// 返回 (各叶子到当前节点的距离列表, 好叶子节点对的数量)
    /// 距离列表只保留 ≤ distance 的值（更远的叶子不可能与另一侧构成好对）
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, d: i32) -> (Vec<i32>, i32) {
        let Some(n) = node else { return (Vec::new(), 0) };
        let n = n.borrow();

        // 叶子节点
        if n.left.is_none() && n.right.is_none() {
            return (vec![0], 0);
        }

        let (left, mut cnt_l) = Self::dfs(&n.left, d);
        let (right, cnt_r) = Self::dfs(&n.right, d);
        let mut cnt = cnt_l + cnt_r;

        // 统计跨越左右子树的好对：左叶子距离 + 右叶子距离 + 2（两条边）≤ distance
        for ld in &left {
            for rd in &right {
                if ld + rd + 2 <= d {
                    cnt += 1;
                }
            }
        }

        // 合并左右距离，+1 表示向上延伸到当前节点
        let mut dists: Vec<i32> = left.into_iter()
            .chain(right)
            .map(|x| x + 1)
            .filter(|&x| x <= d) // 剪枝：超过 distance 的不再向上传递
            .collect();

        (dists, cnt)
    }
}
```
