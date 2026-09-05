---
title: "leetcode-二叉树43"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的垂序遍历

给你二叉树的根结点 root ，请你设计算法计算二叉树的 垂序遍历 序列。

对位于 (row, col) 的每个结点而言，其左右子结点分别位于 (row + 1, col - 1) 和 (row + 1, col + 1) 。树的根结点位于 (0, 0) 。

二叉树的 垂序遍历 从最左边的列开始直到最右边的列结束，按列索引每一列上的所有结点，形成一个按出现位置从上到下排序的有序列表。如果同行同列上有多个结点，则按结点的值从小到大进行排序。

返回二叉树的 垂序遍历 序列。


```
use std::cell::RefCell;
use std::collections::BTreeMap;
use std::rc::Rc;

impl Solution {
    /// BFS 遍历二叉树，记录每个节点的 (row, col, val)。
    /// 用 BTreeMap 按列索引分组，每列内按 (row, val) 排序后输出。
    pub fn vertical_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut map: BTreeMap<i32, Vec<(i32, i32)>> = BTreeMap::new(); // col -> [(row, val)]
        let mut queue = Vec::new();

        if let Some(r) = root {
            queue.push((r, 0, 0)); // (node, col, row)
        }

        // 层序遍历
        while !queue.is_empty() {
            let mut next = Vec::new();
            for (node, col, row) in queue {
                let n = node.borrow();
                map.entry(col).or_default().push((row, n.val));

                if let Some(left) = n.left.clone() {
                    next.push((left, col - 1, row + 1));
                }
                if let Some(right) = n.right.clone() {
                    next.push((right, col + 1, row + 1));
                }
            }
            queue = next;
        }

        // 按列输出，每列内先按 row 再按 val 排序
        map.into_values()
            .map(|mut col_vals| {
                col_vals.sort_unstable();
                col_vals.into_iter().map(|(_, val)| val).collect()
            })
            .collect()
    }
}
```
