---
title: "leetcode-二叉搜索树24"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 两棵二叉搜索树中的所有元素


给你 root1 和 root2 这两棵二叉搜索树。请你返回一个列表，其中包含 两棵树 中的所有整数并按 升序 排序。


```
use std::rc::Rc;
use std::cell::RefCell;

/// 二叉树的中序遍历迭代器（非递归，用栈）
struct InOrderIter {
    stack: Vec<Rc<RefCell<TreeNode>>>,
}

impl InOrderIter {
    fn new(root: Option<Rc<RefCell<TreeNode>>>) -> Self {
        let mut iter = Self { stack: Vec::new() };
        iter.push_left_subtree(root);
        iter
    }

    /// 将当前节点的所有左子节点压栈
    fn push_left_subtree(&mut self, mut node: Option<Rc<RefCell<TreeNode>>>) {
        while let Some(rc) = node {
            self.stack.push(rc.clone());
            node = rc.borrow().left.clone();
        }
    }
}

impl Iterator for InOrderIter {
    type Item = i32;

    fn next(&mut self) -> Option<Self::Item> {
        let rc = self.stack.pop()?;
        let node = rc.borrow();
        let val = node.val;
        // 将右子树的左链压栈
        self.push_left_subtree(node.right.clone());
        Some(val)
    }
}

/// 归并两个有序迭代器
struct MergeIter<I1: Iterator<Item = i32>, I2: Iterator<Item = i32>> {
    iter1: I1,
    iter2: I2,
    peek1: Option<i32>,
    peek2: Option<i32>,
}

impl<I1: Iterator<Item = i32>, I2: Iterator<Item = i32>> MergeIter<I1, I2> {
    fn new(mut iter1: I1, mut iter2: I2) -> Self {
        let peek1 = iter1.next();
        let peek2 = iter2.next();
        Self { iter1, iter2, peek1, peek2 }
    }
}

impl<I1: Iterator<Item = i32>, I2: Iterator<Item = i32>> Iterator for MergeIter<I1, I2> {
    type Item = i32;

    fn next(&mut self) -> Option<Self::Item> {
        match (self.peek1, self.peek2) {
            (Some(v1), Some(v2)) => {
                if v1 < v2 {
                    self.peek1 = self.iter1.next();
                    Some(v1)
                } else {
                    self.peek2 = self.iter2.next();
                    Some(v2)
                }
            }
            (Some(v1), None) => {
                self.peek1 = self.iter1.next();
                Some(v1)
            }
            (None, Some(v2)) => {
                self.peek2 = self.iter2.next();
                Some(v2)
            }
            (None, None) => None,
        }
    }
}

impl Solution {
    /// 纯函数式风格：使用迭代器惰性遍历两棵树，归并后收集为 Vec。
    pub fn get_all_elements(root1: Option<Rc<RefCell<TreeNode>>>, root2: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let iter1 = InOrderIter::new(root1);
        let iter2 = InOrderIter::new(root2);
        MergeIter::new(iter1, iter2).collect()
    }
}
```
