---
title: "leetcode-链表32"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 反转偶数长度组的节点

给你一个链表的头节点 head 。

链表中的节点 按顺序 划分成若干 非空 组，这些非空组的长度构成一个自然数序列（1, 2, 3, 4, ...）。一个组的 长度 就是组中分配到的节点数目。换句话说：

节点 1 分配给第一组
节点 2 和 3 分配给第二组
节点 4、5 和 6 分配给第三组，以此类推

注意，最后一组的长度可能小于或者等于 1 + 倒数第二组的长度 。

反转 每个 偶数 长度组中的节点，并返回修改后链表的头节点 head 。


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
    /// 按自然数序列分组（1,2,3,4,...），反转所有偶数长度组中的节点
    ///
    /// # 算法思路
    /// 1. 按 1,2,3,4,... 的规律将链表分组
    /// 2. 每组单独存入 Vec<Vec<Box<ListNode>>> 中
    /// 3. 检查每组长度，偶数长度则反转
    /// 4. 重建链表
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为链表长度
    /// - 空间：O(n)，存储所有节点
    pub fn reverse_even_length_groups(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 按组收集节点
        let mut groups: Vec<Vec<Box<ListNode>>> = Vec::new();
        let mut current = head;
        let mut group_index = 0; // 当前组索引（从 0 开始，表示第 1 组）
        let mut group_size = 1; // 当前组期望大小

        // 遍历链表，将节点分配到各组
        while let Some(mut node) = current {
            current = node.next.take();

            // 如果当前组已满或不存在，创建新组
            if group_index >= groups.len() {
                groups.push(Vec::new());
            }

            // 将节点加入当前组
            groups[group_index].push(node);

            // 如果当前组达到期望大小，移动到下一组
            if groups[group_index].len() >= group_size {
                group_index += 1;
                group_size += 1;
            }
        }

        // 处理最后一组：如果长度小于等于前一组长度（正常情况），保留原样
        // 反转所有偶数长度的组
        for group in &mut groups {
            if group.len() % 2 == 0 {
                group.reverse();
            }
        }

        // 重建链表
        let mut head = None;
        let mut tail = &mut head;

        for group in groups {
            for mut node in group {
                node.next = None;
                tail = &mut tail.insert(node).next;
            }
        }

        head
    }
}
```
