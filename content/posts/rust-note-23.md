---
title: "Rust Note 23"
date: 2023-06-04T21:15:11+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 反转链表

给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。

### C 解法：
```
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn reverse_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut pre = None;
        let mut head = head;
        while let Some(mut node) = head {
            head = node.next.take();
            node.next = pre;
            pre = Some(node);
        }
        pre
    }
}
```