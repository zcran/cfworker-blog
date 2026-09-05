---
title: "leetcode-二叉树81"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


##  二叉树任务调度

任务调度优化是计算机性能优化的关键任务之一。在任务众多时，不同的调度策略可能会得到不同的总体执行时间，因此寻求一个最优的调度方案是非常有必要的。

通常任务之间是存在依赖关系的，即对于某个任务，你需要先完成他的前导任务（如果非空），才能开始执行该任务。我们保证任务的依赖关系是一棵二叉树，其中 root 为根任务，root.left 和 root.right 为他的两个前导任务（可能为空），root.val 为其自身的执行时间。

在一个 CPU 核执行某个任务时，我们可以在任何时刻暂停当前任务的执行，并保留当前执行进度。在下次继续执行该任务时，会从之前停留的进度开始继续执行。暂停的时间可以不是整数。

现在，系统有两个 CPU 核，即我们可以同时执行两个任务，但是同一个任务不能同时在两个核上执行。给定这颗任务树，请求出所有任务执行完毕的最小时间。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 双核二叉树任务调度最小完成时间
    ///
    /// 每个节点需等左右子树完成后才能执行自身。任务可在任意时刻暂停/恢复。
    /// 对每棵子树，返回 (t1, t2) 表示两个核执行该子树的时间（t1 >= t2）。
    ///
    /// 合并策略：将左右子树的负载分配到两个核上，尽量均衡，
    /// 然后由较忙的核执行当前节点。
    pub fn minimal_exec_time(root: Option<Rc<RefCell<TreeNode>>>) -> f64 {
        Self::dfs(root).0
    }

    /// 返回 (core1_time, core2_time)，保证 core1_time >= core2_time
    fn dfs(root: Option<Rc<RefCell<TreeNode>>>) -> (f64, f64) {
        let node = match root {
            Some(n) => n,
            None => return (0.0, 0.0),
        };

        let mut node_ref = node.borrow_mut();
        let (l1, l2) = Self::dfs(node_ref.left.take());
        let (r1, r2) = Self::dfs(node_ref.right.take());
        let val = node_ref.val as f64;
        drop(node_ref); // 提前释放借用

        let left_sum = l1 + l2;
        let right_sum = r1 + r2;

        // 情况1：左子树可完全嵌入右子树的"空闲间隙"
        if left_sum + r2 <= r1 {
            return (r1 + val, left_sum + r2);
        }
        // 情况2：右子树可完全嵌入左子树的"空闲间隙"
        if right_sum + l2 <= l1 {
            return (l1 + val, right_sum + l2);
        }

        // 情况3：需要负载均衡，理想状态下两核各承担一半总工作量
        let total = left_sum + right_sum;
        let balanced = total / 2.0;
        (balanced + val, balanced)
    }
}
```
