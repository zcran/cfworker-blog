---
title: "leetcode-二叉树53"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


##  二叉树着色游戏

有两位极客玩家参与了一场「二叉树着色」的游戏。游戏中，给出二叉树的根节点 root，树上总共有 n 个节点，且 n 为奇数，其中每个节点上的值从 1 到 n 各不相同。

最开始时：

「一号」玩家从 [1, n] 中取一个值 x（1 <= x <= n）；
「二号」玩家也从 [1, n] 中取一个值 y（1 <= y <= n）且 y != x。

「一号」玩家给值为 x 的节点染上红色，而「二号」玩家给值为 y 的节点染上蓝色。

之后两位玩家轮流进行操作，「一号」玩家先手。每一回合，玩家选择一个被他染过色的节点，将所选节点一个 未着色 的邻节点（即左右子节点、或父节点）进行染色（「一号」玩家染红色，「二号」玩家染蓝色）。

如果（且仅在此种情况下）当前玩家无法找到这样的节点来染色时，其回合就会被跳过。

若两个玩家都没有可以染色的节点时，游戏结束。着色节点最多的那位玩家获得胜利 ✌️。

现在，假设你是「二号」玩家，根据所给出的输入，假如存在一个 y 值可以确保你赢得这场游戏，则返回 true ；若无法获胜，就请返回 false 。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 二号玩家获胜条件：存在某个区域，其节点数超过其余两个区域之和。
    /// 以 x 为界，树分为三个区域：x 的父节点方向、左子树、右子树。
    /// x 自身已被一号玩家占据，不计入任何区域。
    pub fn btree_game_winning_move(root: Option<Rc<RefCell<TreeNode>>>, n: i32, x: i32) -> bool {
        let mut regions = [0; 3]; // [parent_side, left_subtree, right_subtree]
        Self::dfs(&root, x, &mut regions, 0);
        regions.iter().any(|&cnt| cnt * 2 > n - 1)
    }

    /// DFS 遍历。flag 标记当前节点所属区域：
    /// 0 = x 的父节点方向，1 = x 的左子树，2 = x 的右子树。
    /// 遇到 x 时切换标记，x 自身不计入。
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, x: i32, regions: &mut [i32; 3], flag: usize) {
        let Some(n) = node else { return };
        let n = n.borrow();

        if n.val == x {
            Self::dfs(&n.left, x, regions, 1);
            Self::dfs(&n.right, x, regions, 2);
            return;
        }

        regions[flag] += 1;
        Self::dfs(&n.left, x, regions, flag);
        Self::dfs(&n.right, x, regions, flag);
    }
}
```
