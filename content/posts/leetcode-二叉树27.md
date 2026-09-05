---
title: "leetcode-二叉树27"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的层平均值

给定一个非空二叉树的根节点 root , 以数组的形式返回每一层节点的平均值。与实际答案相差 10-5 以内的答案可以被接受。


```
use std::cell::RefCell;
use std::collections::VecDeque;
use std::rc::Rc;

impl Solution {
    /// 返回二叉树每层节点的平均值
    ///
    /// BFS 层序遍历，每层累加求和后除以节点数。
    /// 时间复杂度 O(n)，空间复杂度 O(w)，w 为最大宽度。
    pub fn average_of_levels(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<f64> {
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();

        if let Some(node) = root {
            queue.push_back(node);
        }

        while !queue.is_empty() {
            let level_size = queue.len();
            let mut sum: i64 = 0;

            for _ in 0..level_size {
                let node = queue.pop_front().unwrap();
                let n = node.borrow();
                sum += n.val as i64;

                if let Some(left) = &n.left {
                    queue.push_back(left.clone());
                }
                if let Some(right) = &n.right {
                    queue.push_back(right.clone());
                }
            }

            ans.push(sum as f64 / level_size as f64);
        }

        ans
    }
}
```
