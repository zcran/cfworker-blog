---
title: "leetcode-二叉树42"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 在二叉树中分配硬币

给你一个有 n 个结点的二叉树的根结点 root ，其中树中每个结点 node 都对应有 node.val 枚硬币。整棵树上一共有 n 枚硬币。

在一次移动中，我们可以选择两个相邻的结点，然后将一枚硬币从其中一个结点移动到另一个结点。移动可以是从父结点到子结点，或者从子结点移动到父结点。

返回使每个结点上 只有 一枚硬币所需的 最少 移动次数。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 后序遍历：计算每棵子树需要净流出（或流入）的硬币数。
    /// 设节点需要平衡的硬币数为 node.val - 1，子树的净流出量为：
    ///     左子树流出 + 右子树流出 + 当前节点需平衡数
    /// 移动次数为各子树净流出量的绝对值之和，因为每枚硬币跨一条边移动一次。
    pub fn distribute_coins(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut moves = 0;

        // 返回以 node 为根的子树的净流出量（正数表示需要向外给，负数表示需要从外面收）
        fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, moves: &mut i32) -> i32 {
            let Some(n) = node else { return 0 };
            let n = n.borrow();

            let left_flow = dfs(&n.left, moves);   // 左子树净流出
            let right_flow = dfs(&n.right, moves); // 右子树净流出

            // 经过当前节点到左右子树的边，需要移动的硬币数
            *moves += left_flow.abs() + right_flow.abs();

            // 当前子树总净流出 = 左右子树流出 + 当前节点盈余（val - 1 为需要分出去的）
            n.val - 1 + left_flow + right_flow
        }

        dfs(&root, &mut moves);
        moves
    }
}
```
