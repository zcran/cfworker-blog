---
title: "leetcode-二叉树83"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 开幕式焰火

「力扣挑战赛」开幕式开始了，空中绽放了一颗二叉树形的巨型焰火。 给定一棵二叉树 root 代表焰火，节点值表示巨型焰火这一位置的颜色种类。请帮小扣计算巨型焰火有多少种不同的颜色。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashSet;

impl Solution {
    /// 统计二叉树中不同颜色（节点值）的数量
    ///
    /// 用迭代 DFS 遍历整棵树，将所有节点值收集到 HashSet 中自动去重。
    pub fn num_color(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut colors = HashSet::new();
        let mut stack = vec![root];

        while let Some(node) = stack.pop() {
            if let Some(n) = node {
                let n = n.borrow();
                colors.insert(n.val);
                stack.push(n.left.clone());
                stack.push(n.right.clone());
            }
        }

        colors.len() as i32
    }
}
```
