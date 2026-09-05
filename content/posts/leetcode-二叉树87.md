---
title: "leetcode-二叉树87"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 在每个树行中找最大值

给定一棵二叉树的根节点 root ，请找出该二叉树中每一层的最大值。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 返回二叉树每一层的最大值。
    ///
    /// 使用 BFS 层序遍历，逐层统计最大值。
    /// 时间复杂度 O(n)，空间复杂度 O(w)，其中 w 为树的最大宽度。
    pub fn largest_values(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();

        if let Some(node) = root {
            queue.push_back(node);
        }

        while !queue.is_empty() {
            let level_size = queue.len();
            // 取该层第一个节点的值作为初始最大值
            let mut level_max = queue.front().unwrap().borrow().val;

            for _ in 0..level_size {
                let node = queue.pop_front().unwrap();
                let n = node.borrow();
                level_max = level_max.max(n.val);

                if let Some(left) = n.left.as_ref() {
                    queue.push_back(left.clone());
                }
                if let Some(right) = n.right.as_ref() {
                    queue.push_back(right.clone());
                }
            }

            ans.push(level_max);
        }

        ans
    }
}
```
