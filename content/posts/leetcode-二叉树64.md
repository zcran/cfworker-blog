---
title: "leetcode-二叉树64"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 奇偶树

如果一棵二叉树满足下述几个条件，则可以称为 奇偶树 ：

二叉树根节点所在层下标为 0 ，根的子节点所在层下标为 1 ，根的孙节点所在层下标为 2 ，依此类推。
偶数下标 层上的所有节点的值都是 奇 整数，从左到右按顺序 严格递增
奇数下标 层上的所有节点的值都是 偶 整数，从左到右按顺序 严格递减

给你二叉树的根节点，如果二叉树为 奇偶树 ，则返回 true ，否则返回 false 。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    pub fn is_even_odd_tree(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        let mut q = VecDeque::new();
        q.push_back(root.unwrap());
        let mut is_even = true; // 当前层下标是否为偶数

        while !q.is_empty() {
            let size = q.len();
            // 偶数层：严格递增，最小值前驱为 -1；奇数层：严格递减，最大值前驱为 +∞
            let mut prev = if is_even { -1 } else { i32::MAX };

            for _ in 0..size {
                let node = q.pop_front().unwrap();
                let val = node.borrow().val;

                // 偶数层：值必须为奇数且严格递增
                // 奇数层：值必须为偶数且严格递减
                if is_even {
                    if val <= prev || val & 1 == 0 { return false; }
                } else {
                    if val >= prev || val & 1 == 1 { return false; }
                }
                prev = val;

                // 子节点入队，下一层奇偶性自动翻转
                if let Some(left) = node.borrow().left.clone() {
                    q.push_back(left);
                }
                if let Some(right) = node.borrow().right.clone() {
                    q.push_back(right);
                }
            }
            is_even = !is_even;
        }
        true
    }
}
```
