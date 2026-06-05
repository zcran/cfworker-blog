---
title: "leetcode-递归45"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 链表求和

给定两个用链表表示的整数，每个节点包含一个数位。

这些数位是反向存放的，也就是个位排在链表首部。

编写函数对这两个整数求和，并用链表形式返回结果。


```
impl Solution {
    // 链表求和：两个整数反向存放在链表中（个位在链表头部）
    // 例如：整数 342 表示为 2 -> 4 -> 3
    pub fn add_two_numbers(
        mut l1: Option<Box<ListNode>>,  // 第一个整数的链表表示（反向存放）
        mut l2: Option<Box<ListNode>>,  // 第二个整数的链表表示（反向存放）
    ) -> Option<Box<ListNode>> {        // 返回两个整数之和的链表表示（也是反向存放）

        // 创建哑节点（dummy node），简化链表构建操作
        // 实际结果链表从 dummy.next 开始
        let mut dummy = ListNode::new(-1);

        // 进位值：两个数位相加可能产生进位（0 或 1）
        // 例如：7+8=15，进位为1
        let mut carry = 0;

        // cur 指向当前要插入新节点的位置
        // 初始指向 dummy.next 的位置
        let mut cur = &mut dummy.next;

        // 遍历两个链表，直到两个链表都为空
        // 注意：两个链表长度可能不同，较短的链表高位视为0
        while l1.is_some() || l2.is_some() {

            // 如果 l1 还有节点，取出当前节点的数位值并累加到进位
            if let Some(node) = l1.take() {
                carry += node.val;   // 加上该位的数值
                l1 = node.next;      // l1 指针移动到下一位
            }

            // 如果 l2 还有节点，取出当前节点的数位值并累加到进位
            if let Some(node) = l2.take() {
                carry += node.val;   // 加上该位的数值
                l2 = node.next;      // l2 指针移动到下一位
            }

            // 创建新节点：当前位的数值 = 两数位之和 + 进位 的个位数
            // 例如：7+8=15，当前位存入5
            *cur = Some(Box::new(ListNode::new(carry % 10)));

            // 移动 cur 指针到新节点的 next 位置，准备处理下一位
            cur = &mut cur.as_mut().unwrap().next;

            // 更新进位：去掉当前位的数值，保留十位数作为下一位的进位
            // 例如：15 -> 15/10=1，进位为1
            carry /= 10;
        }

        // 所有位处理完毕后，如果还有进位（例如：5+5=10，最后进位1）
        // 需要在高位补一个新节点存储进位值
        if carry > 0 {
            *cur = Some(Box::new(ListNode::new(carry)));
        }

        // 返回结果链表（跳过哑节点）
        dummy.next.take()
    }
}
```
