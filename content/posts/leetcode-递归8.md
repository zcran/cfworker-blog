---
title: "leetcode-递归8"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 重排链表

给定一个单链表 L 的头节点 head ，单链表 L 表示为：

L0 → L1 → … → Ln - 1 → Ln
请将其重新排列后变为：

L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。



```
impl Solution {
    pub fn reorder_list(head: &mut Option<Box<ListNode>>) {
        // 如果链表为空或只有一个节点，无需重排序
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            return;
        }

        // 取得链表所有权（head 暂时变为 None）
        let mut head_take = head.take();

        // 第一步：计算链表长度
        let mut len = 0;
        let mut p = head_take.as_ref();  // 不可变引用遍历
        while let Some(n) = p {
            len += 1;
            p = n.next.as_ref();
        }

        // 第二步：将链表分为前后两半
        let mut c = len / 2;  // 前半段包含的节点数
        let mut dummy = Box::new(ListNode::new(0));  // 虚拟头节点，用于构建前半段
        let mut prev = &mut dummy;  // 当前节点的可变引用
        let mut half = head_take;   // half 会变成后半段的头节点

        while let Some(mut n) = half {
            c -= 1;
            if c < 0 {
                // 已经取了足够的前半段节点，剩余的是后半段
                half = Some(n);
                break;
            }
            // 取出 n 的下一个节点
            let mut next = n.next.take();
            // 将 n 连接到前半段
            prev.next = Some(n);
            prev = prev.next.as_mut().unwrap();
            half = next;  // 继续处理下一个节点
        }

        // 第三步：反转后半段链表
        let mut prev = None;
        let mut bottom = half;  // bottom 是待反转的后半段
        while let Some(mut n) = bottom {
            let next = n.next.take();
            n.next = prev;
            prev = Some(n);  // prev 成为反转后的头节点
            bottom = next;
        }

        // 第四步：交错合并前后两段
        let mut p1 = dummy.next.take();  // 前半段头节点
        let mut p2 = prev;               // 后半段头节点（已反转）
        let mut prev = dummy.as_mut();   // 指向虚拟头节点，用于构建结果

        loop {
            match (p1, p2) {
                (Some(mut n1), Some(mut n2)) => {
                    // 保存各自的下一个节点
                    let n1_next = n1.next.take();
                    let n2_next = n2.next.take();

                    // 拼接：n1 -> n2
                    n1.next = Some(n2);
                    prev.next = Some(n1);

                    // 移动 prev 到 n2，为下一次插入做准备
                    prev = prev.next.as_mut().unwrap().next.as_mut().unwrap();

                    // 移动指针到下一个待处理的节点
                    p1 = n1_next;
                    p2 = n2_next;
                }
                (Some(n), None) | (None, Some(n)) => {
                    // 某一段还有剩余节点，直接连接
                    prev.next = Some(n);
                    break;
                }
                _ => {
                    // 两段都为空
                    break;
                }
            }
        }

        // 第五步：将结果放回 head
        *head = dummy.next;
    }
}
```
