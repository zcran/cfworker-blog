---
title: "leetcode-二叉搜索树33"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


##  二叉搜索树迭代器

实现一个二叉搜索树迭代器类BSTIterator ，表示一个按中序遍历二叉搜索树（BST）的迭代器：

· BSTIterator(TreeNode root) 初始化 BSTIterator 类的一个对象。BST 的根节点 root 会作为构造函数的一部分给出。指针应初始化为一个不存在于 BST 中的数字，且该数字小于 BST 中的任何元素。

· boolean hasNext() 如果向指针右侧遍历存在数字，则返回 true ；否则返回 false 。

· int next()将指针向右移动，然后返回指针处的数字。

注意，指针初始化为一个不存在于 BST 中的数字，所以对 next() 的首次调用将返回 BST 中的最小元素。

可以假设 next() 调用总是有效的，也就是说，当调用 next() 时，BST 的中序遍历中至少存在一个下一个数字。

```
use std::rc::Rc;
use std::cell::RefCell;

struct BSTIterator {
    stack: Vec<Rc<RefCell<TreeNode>>>,
}

impl BSTIterator {
    /// 构造迭代器：将根节点及其所有左子节点压栈（形成中序遍历的起点）
    fn new(root: Option<Rc<RefCell<TreeNode>>>) -> Self {
        let mut stack = Vec::new();
        let mut node = root;
        // 压入左链
        while let Some(n) = node {
            stack.push(n.clone());
            node = n.borrow().left.clone();
        }
        BSTIterator { stack }
    }

    /// 返回下一个最小的整数值（假设调用前 has_next 为 true）
    fn next(&mut self) -> i32 {
        let node = self.stack.pop().unwrap();
        let val = node.borrow().val;

        // 处理右子树：将右子节点的左链全部压栈
        let mut node = node.borrow().right.clone();
        while let Some(n) = node {
            self.stack.push(n.clone());
            node = n.borrow().left.clone();
        }
        val
    }

    /// 判断是否还有下一个元素
    fn has_next(&self) -> bool {
        !self.stack.is_empty()
    }
}
```
