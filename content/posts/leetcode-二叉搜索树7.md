---
title: "leetcode-二叉搜索树7"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



## 二叉搜索树迭代器


实现一个二叉搜索树迭代器类BSTIterator ，表示一个按中序遍历二叉搜索树（BST）的迭代器：

· BSTIterator(TreeNode root) 初始化 BSTIterator 类的一个对象。BST 的根节点 root 会作为构造函数的一部分给出。指针应初始化为一个不存在于 BST 中的数字，且该数字小于 BST 中的任何元素。

· boolean hasNext() 如果向指针右侧遍历存在数字，则返回 true ；否则返回 false 。

· int next()将指针向右移动，然后返回指针处的数字。


注意，指针初始化为一个不存在于 BST 中的数字，所以对 next() 的首次调用将返回 BST 中的最小元素。

你可以假设 next() 调用总是有效的，也就是说，当调用 next() 时，BST 的中序遍历中至少存在一个下一个数字。


```
use std::rc::Rc;
use std::cell::RefCell;

/// BST 迭代器：按升序遍历二叉搜索树（中序遍历）
struct BSTIterator {
    stack: Vec<Rc<RefCell<TreeNode>>>,
}

impl BSTIterator {
    /// 创建迭代器：初始化时将所有最左路径上的节点压入栈中
    fn new(root: Option<Rc<RefCell<TreeNode>>>) -> Self {
        let mut stack = Vec::new();
        let mut node = root;
        // 沿着左子树一直深入，将沿途节点压栈
        while let Some(n) = node {
            stack.push(n.clone());
            node = n.borrow().left.clone();
        }
        BSTIterator { stack }
    }

    /// 返回下一个最小的元素
    fn next(&mut self) -> i32 {
        // 栈顶即为当前最小节点
        let node = self.stack.pop().unwrap();
        let val = node.borrow().val;

        // 处理右子树：将该节点的右子节点及其所有左孩子压栈
        let mut node = node.borrow().right.clone();
        while let Some(n) = node {
            self.stack.push(n.clone());
            node = n.borrow().left.clone();
        }
        val
    }

    /// 判断是否还有元素
    fn has_next(&self) -> bool {
        !self.stack.is_empty()
    }
}
```
