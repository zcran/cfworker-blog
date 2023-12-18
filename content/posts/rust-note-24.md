---
title: "Rust Note 24"
date: 2023-06-04T21:15:40+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 合并两个有序链表

将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

### C 解法：
```
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* preHead = new ListNode(-1);

        ListNode* prev = preHead;
        while (l1 != nullptr && l2 != nullptr) {
            if (l1->val < l2->val) {
                prev->next = l1;
                l1 = l1->next;
            } else {
                prev->next = l2;
                l2 = l2->next;
            }
            prev = prev->next;
        }

        // 合并后 l1 和 l2 最多只有一个还未被合并完，我们直接将链表末尾指向未合并完的链表即可
        prev->next = l1 == nullptr ? l2 : l1;

        return preHead->next;
    }
};
```


### Rust 解法：
```
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
    pub fn merge_two_lists(mut list1: Option<Box<ListNode>>, mut list2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut dummy = ListNode::new(-1);
        let mut cur = &mut dummy;
        while let (Some(l1), Some(l2)) = (list1.as_ref(), list2.as_ref()) {
            let l = if l1.val <= l2.val { &mut list1 } else { &mut list2 };
            cur.next = l.take();
            cur = cur.next.as_mut().unwrap();
            *l = cur.next.take();
        }
        cur.next = list1.or(list2);
        dummy.next
    }
}
```

一下是chatgpt的解释：
```
这是一个 Rust 语言实现的合并两个有序链表的函数。函数接收两个参数，分别是两个有序链表的头节点。函数返回合并后的有序链表的头节点。

函数的实现过程如下：

1.创建一个虚拟节点(dummy)，方便操作合并后的链表。

2.创建一个可变引用(cur)指向虚拟节点，用于遍历合并后的链表。

3.进入循环，当两个链表都非空时，比较两个链表头节点的值，将较小的节点接到当前节点(cur)的后面，同时移动指针。

4.如果有一个链表为空，直接将另一个链表接到当前节点(cur)的后面。

5.返回虚拟节点(dummy)的下一个节点，即为合并后的链表的头节点。

该函数的实现采用了 Rust 中比较常用的 Option 和 Box 类型，其中 Option 表示某个值可能为空，Box 表示在堆上分配内存的智能指针。函数的实现方法比较巧妙，通过引用和取值操作避免了不必要的内存分配和数据复制。
```