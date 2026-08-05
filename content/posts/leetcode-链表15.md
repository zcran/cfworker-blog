---
title: "leetcode-链表15"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 相交链表

给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。

图示两个链表在节点 c1 开始相交：

![](../pic/160_statement.png)

题目数据 保证 整个链式结构中不存在环。

注意，函数返回结果后，链表必须 保持其原始结构 。

自定义评测：

评测系统 的输入如下（你设计的程序 不适用 此输入）：

· intersectVal - 相交的起始节点的值。如果不存在相交节点，这一值为 0
· listA - 第一个链表
· listB - 第二个链表
· skipA - 在 listA 中（从头节点开始）跳到交叉节点的节点数
· skipB - 在 listB 中（从头节点开始）跳到交叉节点的节点数

评测系统将根据这些输入创建链式数据结构，并将两个头节点 headA 和 headB 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被 视作正确答案 。



C++
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */

class Solution {
public:
    /**
     * 获取两个单链表的相交节点
     *
     * 算法思路：哈希表法
     * 1. 遍历链表A，将所有节点存入哈希集合
     * 2. 遍历链表B，检查当前节点是否在哈希集合中
     * 3. 如果存在，则当前节点即为相交节点
     *
     * 时间复杂度：O(m + n)，其中 m 和 n 分别是两个链表的长度
     * 空间复杂度：O(m) 或 O(n)，需要存储其中一个链表的所有节点
     */
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        // 使用哈希集合存储链表A中的所有节点
        unordered_set<ListNode *> visitedNodes;

        // 遍历链表A，将所有节点加入哈希集合
        ListNode *current = headA;
        while (current != nullptr) {
            visitedNodes.insert(current);
            current = current->next;
        }

        // 遍历链表B，检查是否有节点在哈希集合中
        current = headB;
        while (current != nullptr) {
            // 如果当前节点已经在哈希集合中，说明这是相交节点
            if (visitedNodes.count(current) > 0) {
                return current;
            }
            current = current->next;
        }

        // 没有找到相交节点
        return nullptr;
    }
};
```
