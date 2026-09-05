---
title: "leetcode-二叉树76"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树中的第 K 大层和

给你一棵二叉树的根节点 root 和一个正整数 k 。

树中的 层和 是指 同一层 上节点值的总和。

返回树中第 k 大的层和（不一定不同）。如果树少于 k 层，则返回 -1 。

注意，如果两个节点与根节点的距离相同，则认为它们在同一层。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn kth_largest_level_sum(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i64 {
        let mut sums = Vec::new();
        let mut cur = vec![root.unwrap()];

        while !cur.is_empty() {
            let mut next = Vec::with_capacity(cur.len() * 2);
            let mut sum = 0i64;

            for node in cur {
                let n = node.borrow();
                sum += n.val as i64;
                if let Some(ref l) = n.left {
                    next.push(l.clone());
                }
                if let Some(ref r) = n.right {
                    next.push(r.clone());
                }
            }

            sums.push(sum);
            cur = next;
        }

        let k = k as usize;
        if k > sums.len() {
            return -1;
        }

        // 使用 select_nth_unstable 思想：只需第 k 大，无需全排序
        let idx = sums.len() - k;
        let (_, &mut ans, _) = sums.select_nth_unstable(idx);
        ans
    }
}
```
