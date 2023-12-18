---
title: "Rust Note 25"
date: 2023-06-04T21:16:10+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 回文链表

给你一个单链表的头节点 head ，请你判断该链表是否为回文链表。如果是，返回 true ；否则，返回 false 。

### C 解法：
```
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        vector<int> vals;
        while (head != nullptr) {
            vals.emplace_back(head->val);
            head = head->next;
        }
        for (int i = 0, j = (int)vals.size() - 1; i < j; ++i, --j) {
            if (vals[i] != vals[j]) {
                return false;
            }
        }
        return true;
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
    pub fn is_palindrome(head: Option<Box<ListNode>>) -> bool {
        if head == None { return false; }
        let mut vec = Vec::new();
        let mut current = head;
        while let Some(mut tmp) = current.take() {
	        let next = tmp.next.take();
	        vec.push(tmp.val);
	        current = next;
        }
        if vec.len() == 1 {
            return true;
        } else {
            let mut q = vec.len() - 1;
            let n = vec.len() / 2;
            for i in 0..n {
                if vec[i] != vec[q-i] {
                    return false;
                }
            }
        }
        true
    }
}
```