---
title: "leetcode-链表12"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 环形链表 II

给定一个链表的头节点  head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

不允许修改 链表。


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
     * 检测链表中环的入口节点
     *
     * 算法思路：快慢指针（Floyd 判圈算法）
     * 1. 使用快慢指针判断是否存在环
     * 2. 如果存在环，找到环的入口节点
     *    - 当快慢指针第一次相遇时，将其中一个指针移回链表头
     *    - 然后两个指针以相同速度前进，再次相遇的位置即为环的入口
     *
     * 时间复杂度：O(n)，其中 n 是链表节点数
     * 空间复杂度：O(1)，只使用了常数个指针
     */
    ListNode *detectCycle(ListNode *head) {
        // 使用快慢指针检测是否有环
        ListNode* fast = head;  // 快指针：每次走两步
        ListNode* slow = head;  // 慢指针：每次走一步

        // 遍历链表，检测环
        while (fast != nullptr && fast->next != nullptr) {
            fast = fast->next->next;  // 快指针走两步
            slow = slow->next;        // 慢指针走一步

            // 如果快慢指针相遇，说明存在环
            if (fast == slow) {
                // 找到环的入口节点
                ListNode* pointer1 = head;  // 从头开始
                ListNode* pointer2 = fast;  // 从相遇点开始

                // 两个指针以相同速度前进，相遇点即为环的入口
                while (pointer1 != pointer2) {
                    pointer1 = pointer1->next;
                    pointer2 = pointer2->next;
                }

                return pointer1;  // 返回环的入口节点
            }
        }

        // 没有环
        return nullptr;
    }
};
```
