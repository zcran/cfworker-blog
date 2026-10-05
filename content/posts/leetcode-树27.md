---
title: "leetcode-树27"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 二叉树中的最大路径和

路径 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。

路径和 是路径中各节点值的总和。

给定一个二叉树的根节点 root ，返回其 最大路径和，即所有路径上节点值之和的最大值。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 树形 DP（后序遍历）
    ///
    /// 对每个节点，计算以其为“路径最高点”的最大路径和，
    /// 同时向上返回“以该节点为端点、向下延伸”的最大单边路径和。
    ///
    /// 单边贡献：max(0, max(左贡献, 右贡献) + 当前值)
    /// 全局答案：max(全局答案, 左贡献 + 右贡献 + 当前值)
    pub fn max_path_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut ans = i32::MIN;

        // 迭代后序遍历，避免递归深度过大
        // 使用 HashMap 记录每个节点向上返回的单边最大贡献
        let mut gain: std::collections::HashMap<usize, i32> = std::collections::HashMap::new();

        let root = match root {
            Some(r) => r,
            None => return 0,
        };

        // 生成后序顺序
        let mut order = Vec::new();
        let mut stack = vec![root.clone()];
        let mut visited = std::collections::HashSet::new();

        while let Some(node) = stack.pop() {
            let ptr = Rc::as_ptr(&node) as usize;
            if visited.contains(&ptr) {
                order.push(node);
            } else {
                visited.insert(ptr);
                stack.push(node.clone());
                if let Some(right) = node.borrow().right.clone() {
                    stack.push(right);
                }
                if let Some(left) = node.borrow().left.clone() {
                    stack.push(left);
                }
            }
        }

        for node in order {
            let ptr = Rc::as_ptr(&node) as usize;
            let val = node.borrow().val;

            let left_gain = node
                .borrow()
                .left
                .as_ref()
                .map(|l| gain[&(Rc::as_ptr(l) as usize)])
                .unwrap_or(0);
            let right_gain = node
                .borrow()
                .right
                .as_ref()
                .map(|r| gain[&(Rc::as_ptr(r) as usize)])
                .unwrap_or(0);

            // 以当前节点为最高点的路径和
            let path_sum = left_gain + right_gain + val;
            if path_sum > ans {
                ans = path_sum;
            }

            // 向上返回的单边最大贡献
            let best_child = left_gain.max(right_gain);
            gain.insert(ptr, 0.max(best_child + val));
        }

        ans
    }
}
```
