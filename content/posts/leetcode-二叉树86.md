---
title: "leetcode-二叉树86"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 完全二叉树插入器

完全二叉树是每一层（除最后一层外）都是完全填充（即，节点数达到最大，第 n 层有 2n-1 个节点）的，并且所有的节点都尽可能地集中在左侧。

设计一个用完全二叉树初始化的数据结构 CBTInserter，它支持以下几种操作：

CBTInserter(TreeNode root) 使用根节点为 root 的给定树初始化该数据结构；
CBTInserter.insert(int v)  向树中插入一个新节点，节点类型为 TreeNode，值为 v 。使树保持完全二叉树的状态，并返回插入的新节点的父节点的值；
CBTInserter.get_root() 将返回树的根节点。



```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

/// 完全二叉树插入器。
///
/// 维护一棵完全二叉树，支持在保持完全二叉树性质的前提下按层序插入新节点。
/// 核心思想：用队列缓存"还可以容纳子节点"的候选父节点，这些节点按层序排列，
/// 队首即为下一个插入位置的父节点。
struct CBTInserter {
    root: Rc<RefCell<TreeNode>>,
    /// 候选父节点队列（层序），队首节点即为下一个插入位置的父节点
    candidates: VecDeque<Rc<RefCell<TreeNode>>>,
}

impl CBTInserter {
    /// 使用给定的完全二叉树根节点初始化。
    ///
    /// 通过 BFS 层序遍历，收集所有"还可以插入子节点"的节点作为候选队列。
    /// 完全二叉树中这些候选节点必定连续出现在树的最底层及倒数第二层右侧。
    fn new(root: Option<Rc<RefCell<TreeNode>>>) -> Self {
        let root = root.expect("root must not be null");
        let mut candidates = VecDeque::new();
        let mut queue = VecDeque::from([root.clone()]);

        while let Some(node) = queue.pop_front() {
            let n = node.borrow();
            match (n.left.is_some(), n.right.is_some()) {
                // 左右子节点都存在 → 继续向下层遍历
                (true, true) => {
                    queue.push_back(n.left.as_ref().unwrap().clone());
                    queue.push_back(n.right.as_ref().unwrap().clone());
                }
                // 至少有一个空位 → 该节点是候选父节点
                _ => candidates.push_back(node.clone()),
            }
        }

        Self { root, candidates }
    }

    /// 向完全二叉树中插入值为 `v` 的新节点，返回新节点的父节点值。
    ///
    /// 从候选队列队首取节点作为父节点：
    /// - 若父节点左子为空：新节点插入左侧，父节点仍有右空位，继续留在队首
    /// - 若父节点左子已满：新节点插入右侧，父节点已满，移出队列；
    ///   同时父节点的左右子节点成为新的候选父节点
    fn insert(&mut self, v: i32) -> i32 {
        let parent = self.candidates.front().cloned().unwrap();
        let new_node = Rc::new(RefCell::new(TreeNode::new(v)));

        // 单次不可变借用读取所需信息，避免与后续 borrow_mut 冲突
        let (ans, left_is_none) = {
            let n = parent.borrow();
            (n.val, n.left.is_none())
        };

        if left_is_none {
            // 左子为空：插入左侧，父节点仍有右空位，保留在队首
            parent.borrow_mut().left = Some(new_node);
        } else {
            // 左子已满：插入右侧，父节点已满，移出队列
            parent.borrow_mut().right = Some(new_node.clone());
            self.candidates.pop_front();
            // 子节点成为新的候选
            self.candidates.push_back(parent.borrow().left.as_ref().unwrap().clone());
            self.candidates.push_back(new_node);
        }

        ans
    }

    /// 返回当前树的根节点。
    fn get_root(&self) -> Option<Rc<RefCell<TreeNode>>> {
        Some(self.root.clone())
    }
}
```
