---
title: "leetcode-二叉树30"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树最大宽度

给你一棵二叉树的根节点 root ，返回树的 最大宽度 。

树的 最大宽度 是所有层中最大的 宽度 。

每一层的 宽度 被定义为该层最左和最右的非空节点（即，两个端点）之间的长度。将这个二叉树视作与满二叉树结构相同，两端点间会出现一些延伸到这一层的 null 节点，这些 null 节点也计入长度。

题目数据保证答案将会在  32 位 带符号整数范围内。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回二叉树的最大宽度
    ///
    /// 按满二叉树编号，每层宽度 = 最右编号 - 最左编号 + 1。
    /// BFS 层序遍历，用 u64 编号防止溢出。
    /// 时间复杂度 O(n)，空间复杂度 O(w)
    pub fn width_of_binary_tree(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut ans = 0i32;
        let mut level = vec![(0u64, root)];

        while !level.is_empty() {
            ans = ans.max((level.last().unwrap().0 - level[0].0 + 1) as i32);

            let mut next = Vec::with_capacity(level.len() * 2);
            for (idx, node) in level {
                let Some(node) = node else { continue };
                let n = node.borrow();

                if let Some(left) = &n.left {
                    next.push((idx * 2, Some(left.clone())));
                }
                if let Some(right) = &n.right {
                    next.push((idx * 2 + 1, Some(right.clone())));
                }
            }
            level = next;
        }

        ans
    }
}
```
