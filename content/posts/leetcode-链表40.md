---
title: "leetcode-链表40"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 扁平化多级双向链表

多级双向链表中，除了指向下一个节点和前一个节点指针之外，它还有一个子链表指针，可能指向单独的双向链表。这些子列表也可能会有一个或多个自己的子项，依此类推，生成多级数据结构，如下面的示例所示。

给定位于列表第一级的头节点，请扁平化列表，即将这样的多级双向链表展平成普通的双向链表，使所有结点出现在单级双链表中。



C++
```
class Solution {
public:
    Node* flatten(Node* head) {
        function<Node*(Node*)> dfs = [&](Node* node) {
            Node* cur = node;
            // 记录链表的最后一个节点
            Node* last = nullptr;

            while (cur) {
                Node* next = cur->next;
                //  如果有子节点，那么首先处理子节点
                if (cur->child) {
                    Node* child_last = dfs(cur->child);

                    next = cur->next;
                    //  将 node 与 child 相连
                    cur->next = cur->child;
                    cur->child->prev = cur;

                    //  如果 next 不为空，就将 last 与 next 相连
                    if (next) {
                        child_last->next = next;
                        next->prev = child_last;
                    }

                    // 将 child 置为空
                    cur->child = nullptr;
                    last = child_last;
                }
                else {
                    last = cur;
                }
                cur = next;
            }
            return last;
        };

        dfs(head);
        return head;
    }
};
```
