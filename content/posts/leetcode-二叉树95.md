---
title: "leetcode-二叉树90"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 彩灯装饰记录 III

一棵圣诞树记作根节点为 root 的二叉树，节点值为该位置装饰彩灯的颜色编号。请按照如下规则记录彩灯装饰结果：

第一层按照从左到右的顺序记录

除第一层外每一层的记录顺序均与上一层相反。即第一层为从左到右，第二层为从右到左。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

impl Solution {
    /// 按层序返回彩灯编号，奇数层从左到右，偶数层从右到左（锯齿形层序遍历）。
    ///
    /// 使用双端队列，奇数层从队首出队、子节点按先左后右入队尾；
    /// 偶数层从队尾出队、子节点按先右后左入队首。
    pub fn decorate_record(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();

        if let Some(node) = root {
            queue.push_back(node);
        }

        let mut left_to_right = true;

        while !queue.is_empty() {
            let level_size = queue.len();
            let mut level = Vec::with_capacity(level_size);

            for _ in 0..level_size {
                let (val, children) = if left_to_right {
                    let node = queue.pop_front().unwrap();
                    let n = node.borrow();
                    (n.val, [n.left.clone(), n.right.clone()])
                } else {
                    let node = queue.pop_back().unwrap();
                    let n = node.borrow();
                    (n.val, [n.right.clone(), n.left.clone()])
                };
                level.push(val);

                for child in children {
                    if let Some(c) = child {
                        if left_to_right {
                            queue.push_back(c);
                        } else {
                            queue.push_front(c);
                        }
                    }
                }
            }

            ans.push(level);
            left_to_right = !left_to_right;
        }

        ans
    }
}
```
