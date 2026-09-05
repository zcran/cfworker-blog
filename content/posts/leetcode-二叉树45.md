---
title: "leetcode-二叉树45"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 最大二叉树 II

最大树 定义：一棵树，并满足：其中每个节点的值都大于其子树中的任何其他值。

给你最大树的根节点 root 和一个整数 val 。

就像 之前的问题 那样，给定的树是利用 Construct(a) 例程从列表 a（root = Construct(a)）递归地构建的：

如果 a 为空，返回 null 。
否则，令 a[i] 作为 a 的最大元素。创建一个值为 a[i] 的根节点 root 。
root 的左子树将被构建为 Construct([a[0], a[1], ..., a[i - 1]]) 。
root 的右子树将被构建为 Construct([a[i + 1], a[i + 2], ..., a[a.length - 1]]) 。
返回 root 。

请注意，题目没有直接给出 a ，只是给出一个根节点 root = Construct(a) 。

假设 b 是 a 的副本，并在末尾附加值 val。题目数据保证 b 中的值互不相同。

返回 Construct(b) 。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 将 val 插入最大树。由于 val 是 append 到数组末尾，它一定位于原树的右侧。
    /// 策略：沿右子树向下比较，若 val 更大则成为新根，原子树挂为左子树；
    /// 否则继续递归右子树。
    pub fn insert_into_max_tree(
        root: Option<Rc<RefCell<TreeNode>>>,
        val: i32,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        // 空树直接创建新节点
        let Some(node) = root else {
            return Some(Rc::new(RefCell::new(TreeNode::new(val))));
        };

        let mut n = node.borrow_mut();

        if val > n.val {
            // val 更大，成为新根，当前子树整体作为左子树
            drop(n); // 释放 borrow_mut，避免后续 Rc 移动冲突
            let mut new_root = TreeNode::new(val);
            new_root.left = Some(node);
            Some(Rc::new(RefCell::new(new_root)))
        } else {
            // 继续向右子树递归
            n.right = Self::insert_into_max_tree(n.right.take(), val);
            drop(n);
            Some(node)
        }
    }
}
```
