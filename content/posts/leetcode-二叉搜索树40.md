---
title: "leetcode-二叉搜索树40"
date: 2026-05-14T20:16:16+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## BiNode

二叉树数据结构TreeNode可用来表示单向链表（其中left置空，right为下一个链表节点）。实现一个方法，把二叉搜索树转换为单向链表，要求依然符合二叉搜索树的性质，转换操作应是原址的，也就是在原始的二叉搜索树上直接修改。

返回转换后的单向链表的头节点。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 将二叉搜索树转换为按升序排列的单向右子节点链表（BiNode）
    /// 迭代中序遍历，边遍历边修改右指针，左指针置空
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn convert_bi_node(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let dummy = Rc::new(RefCell::new(TreeNode::new(0)));
        let mut prev = dummy.clone();   // 尾指针
        let mut stack = Vec::new();
        let mut curr = root;

        while !stack.is_empty() || curr.is_some() {
            // 压入所有左子节点
            while let Some(node) = curr {
                curr = node.borrow_mut().left.take();
                stack.push(node);
            }
            // 弹出当前最左节点
            if let Some(node) = stack.pop() {
                curr = node.borrow_mut().right.take();
                // 切断左指针（防止循环引用，可选）
                node.borrow_mut().left = None;
                // 将前驱节点的右指针指向当前节点
                prev.borrow_mut().right = Some(node.clone());
                prev = node;            // 移动尾指针
            }
        }
        // 返回虚拟头节点的右子节点（真正的链表头）
        dummy.borrow_mut().right.take()
    }
}
```
