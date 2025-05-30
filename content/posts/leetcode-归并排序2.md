---
title: "Leetcode 归并排序2"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 排序链表

给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

方法一，归并排序
```
impl Solution {
    // 876. 链表的中间结点（快慢指针）
    fn middle_node(head: &Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut fast = head;
        let mut slow = head;
        while fast.is_some() && fast.as_ref()?.next.is_some() {
            slow = &slow.as_ref()?.next;
            fast = &fast.as_ref()?.next.as_ref()?.next;
        }
        // 把 slow 从 &Option<Box<ListNode>> 强转成 &mut Option<Box<ListNode>>
        #[allow(mutable_transmutes)]
        let slow: &mut Option<Box<ListNode>> = unsafe { std::mem::transmute(slow) };
        slow.take() // 断开中间节点和前一个节点的连接
    }

    // 21. 合并两个有序链表（双指针）
    fn merge_two_lists(mut list1: Option<Box<ListNode>>, mut list2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut dummy = ListNode::new(0); // 用哨兵节点简化代码逻辑
        let mut cur = &mut dummy; // cur 指向新链表的末尾
        while let (Some(node1), Some(node2)) = (&list1, &list2) {
            if node1.val < node2.val {
                cur.next = list1.take(); // 把 list1 加到新链表中
                cur = cur.next.as_mut()?;
                list1 = cur.next.take();
            } else { // 注：相等的情况加哪个节点都是可以的
                cur.next = list2.take(); // 把 list2 加到新链表中
                cur = cur.next.as_mut()?;
                list2 = cur.next.take();
            };
        }
        cur.next = list1.or(list2); // 拼接剩余链表
        dummy.next
    }

    pub fn sort_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 如果链表为空或者只有一个节点，无需排序
        if head.is_none() || head.as_ref()?.next.is_none() {
            return head;
        }
        // 找到中间节点 head2，并断开 head2 与其前一个节点的连接
        // 比如 head=[4,2,1,3]，那么 middleNode 调用结束后 head=[4,2] head2=[1,3]
        let head2 = Self::middle_node(&head);
        // 分治
        let head = Self::sort_list(head);
        let head2 = Self::sort_list(head2);
        // 合并
        Self::merge_two_lists(head, head2)
    }
}

```

方法二，时间复杂度由排序决定，是 O(n log n)，但需要额外的 O(n) 空间。

```
impl Solution {
    pub fn sort_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head.is_none() { return None; }
        let node = *(head.unwrap());
        let mut list = Into::<Vec<i32>>::into(node);
        list.sort();
        Some(Box::new(ListNode::from(list)))
    }
}

impl From<Vec<i32>> for ListNode {
    fn from(value: Vec<i32>) -> Self {
        let mut header = None;
        value.iter().rev().for_each(|&num| {
            let mut node = ListNode::new(num);
            node.next = header.take();
            header = Some(Box::new(node));
        });
        *header.unwrap()
    }
}

impl Into<Vec<i32>> for ListNode {
    fn into(mut self) -> Vec<i32> {
        let mut rtn = Vec::new();
        let mut next = self.next.take();
        rtn.push(self.val);

        while let Some(mut node_b) = next {
            rtn.push((*node_b).val);
            next = (*node_b).next.take();
        }

        rtn
    }
}
```