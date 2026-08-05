---
title: "leetcode-链表21"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 设计链表


你可以选择使用单链表或者双链表，设计并实现自己的链表。

单链表中的节点应该具备两个属性：val 和 next 。val 是当前节点的值，next 是指向下一个节点的指针/引用。

如果是双向链表，则还需要属性 prev 以指示链表中的上一个节点。假设链表中的所有节点下标从 0 开始。

实现 MyLinkedList 类：

· MyLinkedList() 初始化 MyLinkedList 对象。
· int get(int index) 获取链表中下标为 index 的节点的值。如果下标无效，则返回 -1 。
· void addAtHead(int val) 将一个值为 val 的节点插入到链表中第一个元素之前。在插入完成后，新节点会成为链表的第一个节点。
· void addAtTail(int val) 将一个值为 val 的节点追加到链表中作为链表的最后一个元素。
· void addAtIndex(int index, int val) 将一个值为 val 的节点插入到链表中下标为 index 的节点之前。如果 index 等于链表的长度，那么该节点会被追加到链表的末尾。如果 index 比长度更大，该节点将 不会插入 到链表中。
· void deleteAtIndex(int index) 如果下标有效，则删除链表中下标为 index 的节点。

```
/// 单链表节点定义
#[derive(Default)]
struct ListNode {
    val: i32,
    next: Option<Box<ListNode>>,
}

impl ListNode {
    #[inline]
    fn new(val: i32) -> Self {
        ListNode { val, next: None }
    }
}

/// 自定义链表实现
///
/// 支持在头部、尾部、指定位置插入和删除节点
/// 所有操作时间复杂度：O(n)，其中 n 为链表长度
#[derive(Default)]
pub struct MyLinkedList {
    head: Option<Box<ListNode>>,
    len: usize, // 缓存链表长度，避免每次遍历计算
}

impl MyLinkedList {
    pub fn new() -> Self {
        MyLinkedList {
            head: None,
            len: 0,
        }
    }

    /// 获取指定索引节点的值
    ///
    /// # 参数
    /// * `index` - 节点下标（从 0 开始）
    ///
    /// # 返回
    /// 如果索引有效，返回节点值；否则返回 -1
    pub fn get(&self, index: i32) -> i32 {
        if index < 0 || index as usize >= self.len {
            return -1;
        }

        let mut curr = &self.head;
        let mut idx = 0;
        while let Some(node) = curr {
            if idx == index as usize {
                return node.val;
            }
            curr = &node.next;
            idx += 1;
        }
        -1 // 理论不会执行到这里
    }

    /// 在链表头部插入节点
    pub fn add_at_head(&mut self, val: i32) {
        self.head = Some(Box::new(ListNode {
            val,
            next: self.head.take(),
        }));
        self.len += 1;
    }

    /// 在链表尾部追加节点
    pub fn add_at_tail(&mut self, val: i32) {
        let mut curr = &mut self.head;
        while let Some(node) = curr {
            curr = &mut node.next;
        }
        *curr = Some(Box::new(ListNode::new(val)));
        self.len += 1;
    }

    /// 在指定索引前插入节点
    ///
    /// # 参数
    /// * `index` - 插入位置下标
    ///   - 如果 index <= 0，插入到头部
    ///   - 如果 index == 链表长度，追加到尾部
    ///   - 如果 index > 链表长度，不插入
    pub fn add_at_index(&mut self, index: i32, val: i32) {
        let idx = index as usize;

        // 边界处理：无效索引
        if idx > self.len {
            return;
        }

        // 插入到头部
        if idx == 0 {
            self.add_at_head(val);
            return;
        }

        // 插入到中间或尾部
        let mut curr = &mut self.head;
        let mut pos = 0;
        while let Some(node) = curr {
            if pos + 1 == idx {
                node.next = Some(Box::new(ListNode {
                    val,
                    next: node.next.take(),
                }));
                self.len += 1;
                return;
            }
            curr = &mut node.next;
            pos += 1;
        }
    }

    /// 删除指定索引的节点
    ///
    /// # 参数
    /// * `index` - 要删除的节点下标
    pub fn delete_at_index(&mut self, index: i32) {
        let idx = index as usize;
        if index < 0 || idx >= self.len {
            return;
        }

        // 删除头节点
        if idx == 0 {
            if let Some(head) = self.head.take() {
                self.head = head.next;
                self.len -= 1;
            }
            return;
        }

        // 删除中间或尾部节点
        let mut curr = &mut self.head;
        let mut pos = 0;
        while let Some(node) = curr {
            if pos + 1 == idx {
                if let Some(next_node) = node.next.take() {
                    node.next = next_node.next;
                    self.len -= 1;
                }
                return;
            }
            curr = &mut node.next;
            pos += 1;
        }
    }
}
```
