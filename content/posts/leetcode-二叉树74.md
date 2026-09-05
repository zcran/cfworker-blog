---
title: "leetcode-二叉树74"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 逐层排序二叉树所需的最少操作数目

给你一个 值互不相同 的二叉树的根节点 root 。

在一步操作中，你可以选择 同一层 上任意两个节点，交换这两个节点的值。

返回每一层按 严格递增顺序 排序所需的最少操作数目。

节点的 层数 是该节点和根节点之间的路径的边数。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    pub fn minimum_operations(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut q = VecDeque::new();
        q.push_back(root.unwrap());
        let mut ans = 0;

        while !q.is_empty() {
            let size = q.len();
            let mut layer = Vec::with_capacity(size);

            // 收集当前层所有节点值，同时子节点入队
            for _ in 0..size {
                let node = q.pop_front().unwrap();
                let n = node.borrow();
                layer.push(n.val);
                if let Some(ref l) = n.left {
                    q.push_back(l.clone());
                }
                if let Some(ref r) = n.right {
                    q.push_back(r.clone());
                }
            }

            ans += Self::min_swaps(layer);
        }

        ans
    }

    /// 将数组变为严格递增所需的最小交换次数 = 总元素数 - 置换环个数
    fn min_swaps(mut a: Vec<i32>) -> i32 {
        let n = a.len();
        // 记录每个值排序后的目标位置
        let mut pos: Vec<usize> = (0..n).collect();
        pos.sort_by_key(|&i| a[i]);

        let mut vis = vec![false; n];
        let mut cycles = 0;

        for i in 0..n {
            if vis[i] {
                continue;
            }
            // 沿着置换环遍历
            let mut j = i;
            while !vis[j] {
                vis[j] = true;
                j = pos[j]; // 当前元素应去的位置
            }
            cycles += 1;
        }

        (n - cycles) as i32
    }
}
```
