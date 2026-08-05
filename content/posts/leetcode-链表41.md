---
title: "leetcode-链表41"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 循环有序列表的插入

给定循环单调非递减列表中的一个点，写一个函数向这个列表中插入一个新元素 insertVal ，使这个列表仍然是循环升序的。

给定的可以是这个列表中任意一个顶点的指针，并不一定是这个列表中最小元素的指针。

如果有多个满足条件的插入位置，可以选择任意一个位置插入新的值，插入后整个列表仍然保持有序。

如果列表为空（给定的节点是 null），需要创建一个循环有序列表并返回这个节点。否则。请返回原先给定的节点。



C++
```
class Solution {
public:
    Node* insert(Node* head, int insertVal) {
        Node *node = new Node(insertVal);
        if (head == nullptr) {
            node->next = node;
            return node;
        }
        if (head->next == head) {
            head->next = node;
            node->next = head;
            return head;
        }
        Node *curr = head, *next = head->next;
        while (next != head) {
            if (insertVal >= curr->val && insertVal <= next->val) {
                break;
            }
            if (curr->val > next->val) {
                if (insertVal > curr->val || insertVal < next->val) {
                    break;
                }
            }
            curr = curr->next;
            next = next->next;
        }
        curr->next = node;
        node->next = next;
        return head;
    }
};
```
