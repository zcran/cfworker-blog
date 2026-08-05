---
title: "leetcode-链表31"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 找出临界点之间的最小和最大距离

链表中的 临界点 定义为一个 局部极大值点 或 局部极小值点 。

如果当前节点的值 严格大于 前一个节点和后一个节点，那么这个节点就是一个  局部极大值点 。

如果当前节点的值 严格小于 前一个节点和后一个节点，那么这个节点就是一个  局部极小值点 。

注意：节点只有在同时存在前一个节点和后一个节点的情况下，才能成为一个 局部极大值点 / 极小值点 。

给你一个链表 head ，返回一个长度为 2 的数组 [minDistance, maxDistance] ，其中 minDistance 是任意两个不同临界点之间的最小距离，maxDistance 是任意两个不同临界点之间的最大距离。如果临界点少于两个，则返回 [-1，-1] 。


```
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }
//
// impl ListNode {
//   #[inline]
//   fn new(val: i32) -> Self {
//     ListNode {
//       next: None,
//       val
//     }
//   }
// }

impl Solution {
    /// 计算链表中所有临界点之间的最小和最大距离
    ///
    /// 临界点：局部极大值（严格大于前后）或局部极小值（严格小于前后）
    ///
    /// # 返回
    /// `[minDistance, maxDistance]`，若临界点少于2个则返回 `[-1, -1]`
    ///
    /// # 算法步骤
    /// 1. 将链表所有值存入 Vec，便于随机访问
    /// 2. 遍历中间节点，记录所有临界点的索引
    /// 3. 根据索引计算最小距离（相邻临界点间距的最小值）和最大距离（首尾临界点间距）
    pub fn nodes_between_critical_points(head: Option<Box<ListNode>>) -> Vec<i32> {
        // 提取所有节点值到数组
        let mut values = Vec::new();
        let mut current = head;
        while let Some(node) = current {
            values.push(node.val);
            current = node.next;
        }

        // 至少需要 3 个节点才可能产生临界点
        if values.len() < 3 {
            return vec![-1, -1];
        }

        // 记录所有临界点的下标（0-based）
        let mut critical_indices = Vec::new();
        for i in 1..values.len() - 1 {
            let prev = values[i - 1];
            let cur = values[i];
            let next = values[i + 1];
            // 局部极大值或局部极小值
            if (cur > prev && cur > next) || (cur < prev && cur < next) {
                critical_indices.push(i as i32);
            }
        }

        // 临界点少于两个，无法计算距离
        if critical_indices.len() < 2 {
            return vec![-1, -1];
        }

        // 最小距离：所有相邻临界点间距的最小值
        let min_distance = critical_indices
            .windows(2)
            .map(|pair| pair[1] - pair[0])
            .min()
            .unwrap();

        // 最大距离：第一个和最后一个临界点之间的距离
        let max_distance = critical_indices.last().unwrap() - critical_indices.first().unwrap();

        vec![min_distance, max_distance]
    }
}
```
