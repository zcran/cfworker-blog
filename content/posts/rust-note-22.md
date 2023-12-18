---
title: "Rust Note 22"
date: 2023-06-04T21:14:10+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 删除链表的倒数第N个节点

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

### Java 解法：
```
 // 回溯后序遍历
 public ListNode removeNthFromEnd(ListNode head, int n) {
    	int traverse = traverse(head, n);
    	if(traverse == n)
    	    return head.next;
    	return head;
    }
    
    private int traverse(ListNode node, int n) {
        if(node == null)
            return 0;
        int num = traverse(node.next, n);
        if(num == n)
            node.next = node.next.next;
        return num + 1;
    }
```


### Rust 解法：
```
 // 快慢指针法
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }
//
// impl ListNode {
//   #[inline]
//   fn new(val: i32) -> Self {
//     ListNode {
//       next: None,
//       val
//     }
//   }
// }
impl Solution {
    pub fn remove_nth_from_end(head: Option<Box<ListNode>>, n: i32) -> Option<Box<ListNode>> {

        let mut dummy = Some(Box::new(ListNode{val: 0, next: head}));
        let mut slow_p = &mut dummy;
        let mut fast_p = &slow_p.clone();

        for _ in 0..=n {
            if let Some(fast_node) = fast_p {
                fast_p = &fast_node.next;
            } else {
                return None;
            }
        }

        while fast_p.is_some() {
            fast_p = &fast_p.as_ref().unwrap().next;
            slow_p = &mut slow_p.as_mut().unwrap().next;
        }

        let remove_p = &mut slow_p.as_mut().unwrap().next;
        slow_p.as_mut().unwrap().next = remove_p.as_mut().unwrap().next.take();

        dummy.unwrap().next
    }
}
```