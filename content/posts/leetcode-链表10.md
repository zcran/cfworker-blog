---
title: "leetcode-链表10"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 随机链表的复制

给你一个长度为 n 的链表，每个节点包含一个额外增加的随机指针 random ，该指针可以指向链表中的任何节点或空节点。

构造这个链表的 深拷贝。 深拷贝应该正好由 n 个 全新 节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 next 指针和 random 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。复制链表中的指针都不应指向原链表中的节点 。

例如，如果原链表中有 X 和 Y 两个节点，其中 X.random --> Y 。那么在复制链表中对应的两个节点 x 和 y ，同样有 x.random --> y 。

返回复制链表的头节点。

用一个由 n 个节点组成的链表来表示输入/输出中的链表。每个节点用一个 [val, random_index] 表示：

val：一个表示 Node.val 的整数。
random_index：随机指针指向的节点索引（范围从 0 到 n-1）；如果不指向任何节点，则为  null 。

你的代码 只 接受原链表的头节点 head 作为传入参数。

C++
```
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;

    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/

class Solution {
public:
    /**
     * 复制带随机指针的链表
     *
     * 算法思路：三遍遍历法（时间复杂度 O(n)，空间复杂度 O(1)）
     * 步骤1：在原链表的每个节点后面插入一个复制节点
     * 步骤2：设置复制节点的 random 指针
     * 步骤3：将原链表和复制链表分离
     */
    Node* copyRandomList(Node* head) {
        // 处理空链表
        if (head == nullptr) {
            return nullptr;
        }

        // ========== 第一步：插入复制节点 ==========
        // 在每个原节点后面创建一个复制节点
        // 例如：A -> B -> C 变为 A -> A' -> B -> B' -> C -> C'
        for (Node* node = head; node != nullptr; node = node->next->next) {
            Node* copyNode = new Node(node->val);      // 创建复制节点
            copyNode->next = node->next;               // 复制节点指向原节点的下一个节点
            node->next = copyNode;                     // 原节点指向复制节点
        }

        // ========== 第二步：设置 random 指针 ==========
        // 复制节点的 random 指向原节点 random 的下一个节点（即复制节点）
        for (Node* node = head; node != nullptr; node = node->next->next) {
            Node* copyNode = node->next;               // 获取对应的复制节点
            // 如果原节点的 random 不为空，复制节点的 random 指向原 random 的复制节点
            copyNode->random = (node->random != nullptr) ? node->random->next : nullptr;
        }

        // ========== 第三步：分离两个链表 ==========
        Node* newHead = head->next;                    // 保存复制链表的头节点

        for (Node* node = head; node != nullptr; node = node->next) {
            Node* copyNode = node->next;               // 获取当前节点的复制节点

            // 恢复原链表的 next 指针（指向原来的下一个节点）
            node->next = copyNode->next;

            // 设置复制节点的 next 指针（指向下一个复制节点）
            copyNode->next = (copyNode->next != nullptr) ? copyNode->next->next : nullptr;
        }

        return newHead;
    }
};
```
