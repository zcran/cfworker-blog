---
title: "leetcode-单调栈11"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 最大二叉树

给定一个不重复的整数数组 nums 。 最大二叉树 可以用下面的算法从 nums 递归地构建:

1.创建一个根节点，其值为 nums 中的最大值。
2.递归地在最大值 左边 的 子数组前缀上 构建左子树。
3.递归地在最大值 右边 的 子数组后缀上 构建右子树。

返回 nums 构建的 最大二叉树 。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 构建最大二叉树
    ///
    /// 核心思路：单调栈 O(n) 解法
    /// - 维护一个单调递减栈，保证栈中节点值从底到顶递减
    /// - 每个新节点不断弹出栈中比它小的节点，并挂载为左子树
    /// - 最后栈底的节点即为整棵树的根
    pub fn construct_maximum_binary_tree(nums: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        let mut stack: Vec<Rc<RefCell<TreeNode>>> = Vec::with_capacity(nums.len());

        for &num in &nums {
            // 创建当前节点
            let curr = Rc::new(RefCell::new(TreeNode::new(num)));

            // 不断弹出栈中比当前值小的节点，挂载为当前节点的左子树
            let mut last_popped = None;
            while let Some(top) = stack.last() {
                if top.borrow().val > num {
                    break;
                }
                last_popped = stack.pop();
            }

            // 最后弹出的节点成为当前节点的左子树
            if let Some(node) = last_popped {
                curr.borrow_mut().left = Some(node);
            }

            // 当前节点成为栈顶节点的右子树
            if let Some(top) = stack.last() {
                top.borrow_mut().right = Some(curr.clone());
            }

            stack.push(curr);
        }

        // 栈底节点即为根节点
        stack.into_iter().next()
    }
}
```
