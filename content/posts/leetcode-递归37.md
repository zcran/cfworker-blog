---
title: "leetcode-递归37"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 重排链表


给定一个单链表 L 的头节点 head ，单链表 L 表示为：

 L0 → L1 → … → Ln-1 → Ln

请将其重新排列后变为：

L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → …

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

```
impl Solution {
    pub fn reorder_list(head: &mut Option<Box<ListNode>>) {
        // 创建一个双端队列，用于存储所有节点
        let mut node_deque = std::collections::VecDeque::new();

        // 将原链表的所有节点"摘下来"，存入双端队列
        // head.take() 会把 head 的所有权拿出来，原 head 变成 None
        let mut cur = head.take();

        // 遍历原链表，把所有节点取出并存入队列
        while let Some(mut node) = cur {
            // 将当前节点的 next 取出（用 take 而非直接赋值，因为需要所有权）
            // 这样 node.next 就变成了 None，相当于把当前节点从原链表中独立出来
            cur = node.next.take();
            // 将当前节点放入队列尾部
            node_deque.push_back(node);
        }

        // 创建一个哑节点（dummy node），方便操作
        // 哑节点可以简化链表操作，避免处理空链表的边界情况
        let mut dummy = ListNode::new(0);
        let mut cur = &mut dummy;

        // flag 用于控制是从队列前面取还是后面取
        // true: 从头取，false: 从尾取
        let mut flag = true;

        // 当队列不为空时，循环构建新链表
        while !node_deque.is_empty() {
            if flag {
                // 从前端取节点（原链表的前半部分正常顺序）
                cur.next = node_deque.pop_front();
            } else {
                // 从后端取节点（原链表的后半部分倒序）
                cur.next = node_deque.pop_back();
            }
            // 将 cur 指向新加入的节点
            // as_deref_mut() 将 Option<Box<T>> 转换为 Option<&mut T>
            // unwrap() 在这里是安全的，因为刚添加了节点，cur.next 一定不为 None
            cur = cur.next.as_deref_mut().unwrap();
            // 切换 flag，下次从另一端取节点
            flag = !flag;
        }

        // 将 head 指向新构建的链表（dummy 的下一个节点）
        *head = dummy.next;
    }
}
```
