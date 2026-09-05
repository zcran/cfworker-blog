---
title: "leetcode-二叉树34"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树中所有距离为 K 的结点

给定一个二叉树（具有根结点 root）， 一个目标结点 target ，和一个整数值 k ，返回到目标结点 target 距离为 k 的所有结点的值的数组。

答案可以以 任何顺序 返回。


```
use std::cell::RefCell;
use std::collections::{HashMap, VecDeque};
use std::rc::Rc;

impl Solution {
    /// 返回距离目标节点 k 的所有节点值
    ///
    /// 先建图（父指针映射），再从 target 做 BFS 找距离为 k 的节点。
    /// 时间复杂度 O(n)，空间复杂度 O(n)
    pub fn distance_k(
        root: Option<Rc<RefCell<TreeNode>>>,
        target: Option<Rc<RefCell<TreeNode>>>,
        k: i32,
    ) -> Vec<i32> {
        let (root, target) = match (root, target) {
            (Some(r), Some(t)) => (r, t),
            _ => return Vec::new(),
        };

        // 建图：节点值 -> 父节点值
        let mut parent = HashMap::new();
        Self::build_parent(&root, None, &mut parent);

        // BFS 从 target 出发，找距离为 k 的节点
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();
        let mut visited = HashMap::new();
        let target_val = target.borrow().val;

        queue.push_back((target_val, 0));
        visited.insert(target_val, true);

        while let Some((val, dist)) = queue.pop_front() {
            if dist == k {
                ans.push(val);
                continue;
            }

            let node = Self::find_node(&root, val).unwrap();
            let n = node.borrow();

            // 三个方向：父节点、左子、右子
            let mut neighbors = Vec::new();
            if let Some(&Some(p)) = parent.get(&val) {
                neighbors.push(p);
            }
            if let Some(ref left) = n.left {
                neighbors.push(left.borrow().val);
            }
            if let Some(ref right) = n.right {
                neighbors.push(right.borrow().val);
            }

            for next_val in neighbors {
                if !visited.contains_key(&next_val) {
                    visited.insert(next_val, true);
                    queue.push_back((next_val, dist + 1));
                }
            }
        }

        ans
    }

    /// 递归建立父指针映射
    fn build_parent(
        node: &Rc<RefCell<TreeNode>>,
        parent: Option<i32>,
        map: &mut HashMap<i32, Option<i32>>,
    ) {
        let val = node.borrow().val;
        map.insert(val, parent);

        if let Some(ref left) = node.borrow().left {
            Self::build_parent(left, Some(val), map);
        }
        if let Some(ref right) = node.borrow().right {
            Self::build_parent(right, Some(val), map);
        }
    }

    /// 根据节点值查找节点（用于 BFS 中重建引用）
    fn find_node(node: &Rc<RefCell<TreeNode>>, val: i32) -> Option<Rc<RefCell<TreeNode>>> {
        let n = node.borrow();
        if n.val == val {
            return Some(node.clone());
        }
        n.left.as_ref().and_then(|l| Self::find_node(l, val))
            .or_else(|| n.right.as_ref().and_then(|r| Self::find_node(r, val)))
    }
}
```
