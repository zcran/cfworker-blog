---
title: "leetcode-二叉树54"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 最大层内元素和

给你一个二叉树的根节点 root。设根节点位于二叉树的第 1 层，而根节点的子节点位于第 2 层，依此类推。

返回总和 最大 的那一层的层号 x。如果有多层的总和一样大，返回其中 最小 的层号 x。


```
use std::cell::RefCell;
use std::collections::VecDeque;
use std::rc::Rc;

impl Solution {
    /// BFS 层序遍历，每层累加节点值并实时比较。
    /// 若当前层和严格大于已记录的最大值，则更新答案层号。
    /// 由于层号从小到大遍历，相等时自然保留最小层号。
    pub fn max_level_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let Some(root) = root else { return 0 };
        let mut queue = VecDeque::new();
        queue.push_back(root);

        let mut max_sum = i32::MIN;
        let mut ans = 1;
        let mut level = 1;

        while !queue.is_empty() {
            let mut level_sum = 0;
            // 固定当前层节点数，避免动态增长影响循环
            for _ in 0..queue.len() {
                let node = queue.pop_front().unwrap();
                let n = node.borrow();
                level_sum += n.val;

                if let Some(left) = &n.left {
                    queue.push_back(left.clone());
                }
                if let Some(right) = &n.right {
                    queue.push_back(right.clone());
                }
            }

            if level_sum > max_sum {
                max_sum = level_sum;
                ans = level;
            }
            level += 1;
        }

        ans
    }
}
```
