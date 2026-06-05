---
title: "leetcode-递归46"
date: 2026-05-26T10:00:19+08:00
tags: ["leetcode", "递归"]
draft: false
---


##  回文链表


编写一个函数，检查输入的链表是否是回文的。



```
impl Solution {
    // 判断链表是否是回文（正读反读都一样）
    // 例如：1 -> 2 -> 3 -> 2 -> 1 是回文
    fn is_palindrome(head: Option<Box<ListNode>>) -> bool {

        // 递归检查函数
        // 参数：
        //   cur_node: 当前遍历到的节点（从前往后）
        //   fronter: 从头部开始移动的指针（双向移动）
        fn recursively_check(
            cur_node: &Option<Box<ListNode>>,
            fronter: &mut &Option<Box<ListNode>>,
        ) -> bool {

            // 如果当前节点不为空，继续递归到链表末尾
            if cur_node.is_some() {
                // 先递归到链表末尾（cur_node 一直移动到最后一个节点）
                // 这里使用 unwrap() 是安全的，因为前面已经检查了 is_some()
                if !recursively_check(&cur_node.as_ref().unwrap().next, fronter) {
                    return false;  // 如果子递归返回 false，直接向上返回 false
                }

                // 递归返回阶段：此时 cur_node 从最后一个节点开始往回走
                // fronter 从第一个节点开始往后走
                // 比较当前节点（从后往前）和 fronter 节点（从前往后）的值
                if &cur_node.as_ref().unwrap().val != &fronter.as_ref().unwrap().val {
                    return false;  // 值不相等，不是回文
                }

                // 值相等，移动 fronter 到下一个节点（继续比较下一对）
                // *fronter 解引用修改外部的 fronter 指针
                // fronter.as_ref().unwrap().next 获取当前 fronter 的下一个节点
                *fronter = &mut &fronter.as_ref().unwrap().next;
            }

            // 所有比较都通过，返回 true
            true
        }

        // 调用递归函数，初始时 cur_node 和 fronter 都指向头节点
        recursively_check(&head, &mut &head)
    }
}
```
