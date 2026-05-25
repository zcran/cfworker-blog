---
title: "leetcode-二叉搜索树6"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 有序链表转换二叉搜索树


给定一个单链表的头节点  head ，其中的元素 按升序排序 ，将其转换为 平衡 二叉搜索树。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 将有序链表转换为高度平衡的二叉搜索树
    ///
    /// # 原理
    /// 1. 计算链表长度，定位中间节点（左中）。
    /// 2. 拆分链表为左半部分、中间节点的值、右半部分。
    /// 3. 递归构造左右子树。
    ///
    /// # 复杂度
    /// - 时间：O(n log n) – 每次递归 O(n) 定位中点，深度 O(log n)
    /// - 空间：O(log n) 递归栈（不计输出树）
    pub fn sorted_list_to_bst(head: Option<Box<ListNode>>) -> Option<Rc<RefCell<TreeNode>>> {
        // 辅助函数：给定链表头，返回 (左链表, 中间节点值, 右链表)
        fn split(mut head: Box<ListNode>) -> (Option<Box<ListNode>>, i32, Option<Box<ListNode>>) {
            // 计算链表长度（不可变借用）
            let len = {
                let mut cur = &head;
                let mut count = 1;
                while let Some(node) = cur.next.as_ref() {
                    count += 1;
                    cur = node;
                }
                count
            };

            // 只有一个节点：返回空左、节点值、空右
            if len == 1 {
                let val = head.val;
                return (None, val, None);
            }

            let mid_idx = len / 2;               // 中间节点索引（左中）
            let mut prev = &mut head;            // prev 将指向中间节点的前驱

            // 移动 prev 到中间节点的前一个节点
            for _ in 0..mid_idx - 1 {
                prev = prev.next.as_mut().unwrap();
            }

            // 取出中间节点，断开连接
            let mid = prev.next.take().unwrap();  // 中间节点
            let val = mid.val;                    // 取出中间节点的值
            let right = mid.next;                 // 右半部分链表头
            (Some(head), val, right)              // 左半部分链表头为 head
        }

        head.map(|head| {
            let (left, val, right) = split(head);
            Rc::new(RefCell::new(TreeNode {
                val,
                left: Self::sorted_list_to_bst(left),
                right: Self::sorted_list_to_bst(right),
            }))
        })
    }
}
```
