---
title: "leetcode-链表37"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 翻倍以链表形式表示的数字


给你一个 非空 链表的头节点 head ，表示一个不含前导零的非负数整数。

将链表 翻倍 后，返回头节点 head 。



```
impl Solution {
    pub fn double_it(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 先反转链表，从低位开始计算
        let mut prev = None;
        while let Some(mut node) = head {
            let next = node.next.take();
            node.next = prev;
            prev = Some(node);
            head = next;
        }
        let mut reversed = prev;

        // 翻倍计算（从低位到高位）
        let mut dummy = Box::new(ListNode::new(0));
        let mut tail = &mut dummy;
        let mut carry = 0;

        while let Some(mut node) = reversed {
            let sum = node.val * 2 + carry;
            tail.next = Some(Box::new(ListNode::new(sum % 10)));
            tail = tail.next.as_mut().unwrap();
            carry = sum / 10;
            reversed = node.next.take();
        }

        // 处理最后的进位
        if carry > 0 {
            tail.next = Some(Box::new(ListNode::new(carry)));
        }

        // 再次反转得到最终结果
        let mut result = dummy.next;
        let mut prev = None;
        while let Some(mut node) = result {
            let next = node.next.take();
            node.next = prev;
            prev = Some(node);
            result = next;
        }
        prev
    }
}
```
