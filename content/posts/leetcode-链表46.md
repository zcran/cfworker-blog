---
title: "leetcode-链表46"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 复杂链表的复制

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。



C++
```
class Solution {
public:
    Node* copyRandomList(Node* head) {
        /* 1. 判断链表是否为空 */
        if(head == nullptr) return nullptr;
        /* 2. 建立map容器 */
        Node* cur = head;
        unordered_map<Node*, Node*> map;
        /* 3. 复制各节点，并建立 “原节点 -> 新节点” 的 Map 映射 */
        while(cur != nullptr) {
            map[cur] = new Node(cur->val);
            cur = cur->next;
        }
        cur = head;
        /* 4. 构建新链表的 next 和 random 指向 */
        while(cur != nullptr) {
            map[cur]->next = map[cur->next];
            map[cur]->random = map[cur->random];
            cur = cur->next;
        }
        /* 5. 返回新链表的头节点 */
        return map[head];
    }
};
```
