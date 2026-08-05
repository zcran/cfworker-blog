---
title: "leetcode-链表50"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 删除中间节点

若链表中的某个节点，既不是链表头节点，也不是链表尾节点，则称其为该链表的「中间节点」。

假定已知链表的某一个中间节点，请实现一种算法，将该节点从链表中删除。

例如，传入节点 c（位于单向链表 a->b->c->d->e->f 中），将其删除后，剩余链表为 a->b->d->e->f


C++
```
class Solution {
public:
    void deleteNode(ListNode* node) {
        // 复制 node.next 到 node
        node->val = node->next->val;
        // 从链表中删除 node.next
        node->next = node->next->next;
    }
};
```
