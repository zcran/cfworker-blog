---
title: "leetcode-二叉树29"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 输出二叉树

给你一棵二叉树的根节点 root ，请你构造一个下标从 0 开始、大小为 m x n 的字符串矩阵 res ，用以表示树的 格式化布局 。构造此格式化布局矩阵需要遵循以下规则：

树的 高度 为 height ，矩阵的行数 m 应该等于 height + 1 。
矩阵的列数 n 应该等于 2^(height+1) - 1 。
根节点 需要放置在 顶行 的 正中间 ，对应位置为 res[0][(n-1)/2] 。
对于放置在矩阵中的每个节点，设对应位置为 res[r][c] ，将其左子节点放置在 res[r+1][c-2^(height-r-1)] ，右子节点放置在 res[r+1][c+2^(height-r-1)] 。
继续这一过程，直到树中的所有节点都妥善放置。
任意空单元格都应该包含空字符串 "" 。

返回构造得到的矩阵 res 。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 将二叉树格式化为字符串矩阵
    ///
    /// 矩阵大小：行数 = 树高，列数 = 2^树高 - 1
    /// 根节点放在顶行正中，子节点按规则偏移。
    pub fn print_tree(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<String>> {
        let height = Self::get_height(&root);
        let cols = (1 << height) - 1;
        let mut grid = vec![vec![String::new(); cols]; height];

        Self::dfs(root, 0, (cols - 1) / 2, height, &mut grid);
        grid
    }

    fn dfs(
        node: Option<Rc<RefCell<TreeNode>>>,
        row: usize,
        col: usize,
        height: usize,
        grid: &mut Vec<Vec<String>>,
    ) {
        let Some(node) = node else { return };

        let n = node.borrow();
        grid[row][col] = n.val.to_string();

        let offset = 1 << (height - row - 2);
        Self::dfs(n.left.clone(), row + 1, col - offset, height, grid);
        Self::dfs(n.right.clone(), row + 1, col + offset, height, grid);
    }

    fn get_height(root: &Option<Rc<RefCell<TreeNode>>>) -> usize {
        let Some(node) = root else { return 0 };
        let n = node.borrow();
        1 + Self::get_height(&n.left).max(Self::get_height(&n.right))
    }
}
```
