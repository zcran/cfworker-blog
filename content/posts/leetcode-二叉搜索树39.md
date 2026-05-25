---
title: "leetcode-二叉搜索树39"
date: 2026-05-14T20:16:16+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索树序列

从左向右遍历一个数组，通过不断将其中的元素插入树中可以逐步地生成一棵二叉搜索树。

给定一个由不同节点组成的二叉搜索树 root，输出所有可能生成此树的数组。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 返回所有可能的 BST 构建序列（数组表示）
    /// 算法：回溯 + 队列维护可用节点集合
    pub fn bst_sequences(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        match root {
            None => vec![vec![]],
            Some(node) => {
                let mut results = Vec::new();
                let mut queue = VecDeque::new();
                let mut path = Vec::new();
                queue.push_back(node);
                Self::backtrack(&mut results, &mut queue, &mut path);
                results
            }
        }
    }

    fn backtrack(
        results: &mut Vec<Vec<i32>>,
        queue: &mut VecDeque<Rc<RefCell<TreeNode>>>,
        path: &mut Vec<i32>,
    ) {
        if queue.is_empty() {
            results.push(path.clone());
            return;
        }

        let level_len = queue.len();
        for _ in 0..level_len {
            let node = queue.pop_front().unwrap();
            // 在一个独立作用域中获取节点值及子节点，随后释放借用
            let (val, left_child, right_child) = {
                let n_ref = node.borrow();
                (n_ref.val, n_ref.left.clone(), n_ref.right.clone())
            }; // n_ref 在此处丢弃，node 不再被借用

            path.push(val);

            // 添加子节点到队列（记录是否真的添加）
            let left_added = if let Some(left) = left_child {
                queue.push_back(left);
                true
            } else {
                false
            };
            let right_added = if let Some(right) = right_child {
                queue.push_back(right);
                true
            } else {
                false
            };

            Self::backtrack(results, queue, path);

            // 回溯：移除刚添加的子节点（后进先出）
            if right_added {
                queue.pop_back();
            }
            if left_added {
                queue.pop_back();
            }
            path.pop();

            // 将当前节点放回队列尾部，供后续排列使用
            queue.push_back(node);
        }
    }
}
```
