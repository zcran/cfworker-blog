---
title: "Leetcode 双向链表4"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---



## LFU 缓存

请你为 最不经常使用（LFU）缓存算法设计并实现数据结构。

实现 LFUCache 类：

LFUCache(int capacity) - 用数据结构的容量 capacity 初始化对象
int get(int key) - 如果键 key 存在于缓存中，则获取键的值，否则返回 -1 。
void put(int key, int value) - 如果键 key 已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量 capacity 时，则应该在插入新项之前，移除最不经常使用的项。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除 最久未使用 的键。
为了确定最不常使用的键，可以为缓存中的每个键维护一个 使用计数器 。使用计数最小的键是最久未使用的键。

当一个键首次插入到缓存中时，它的使用计数器被设置为 1 (由于 put 操作)。对缓存中的键执行 get 或 put 操作，使用计数器的值将会递增。

函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。



示例：

输入：
["LFUCache", "put", "put", "get", "put", "get", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [3], [4, 4], [1], [3], [4]]
输出：
[null, null, null, 1, null, -1, 3, null, -1, 3, 4]

解释：
// cnt(x) = 键 x 的使用计数
// cache=[] 将显示最后一次使用的顺序（最左边的元素是最近的）
LFUCache lfu = new LFUCache(2);
lfu.put(1, 1);   // cache=[1,_], cnt(1)=1
lfu.put(2, 2);   // cache=[2,1], cnt(2)=1, cnt(1)=1
lfu.get(1);      // 返回 1
                 // cache=[1,2], cnt(2)=1, cnt(1)=2
lfu.put(3, 3);   // 去除键 2 ，因为 cnt(2)=1 ，使用计数最小
                 // cache=[3,1], cnt(3)=1, cnt(1)=2
lfu.get(2);      // 返回 -1（未找到）
lfu.get(3);      // 返回 3
                 // cache=[3,1], cnt(3)=2, cnt(1)=2
lfu.put(4, 4);   // 去除键 1 ，1 和 3 的 cnt 相同，但 1 最久未使用
                 // cache=[4,3], cnt(4)=1, cnt(3)=2
lfu.get(1);      // 返回 -1（未找到）
lfu.get(3);      // 返回 3
                 // cache=[3,4], cnt(4)=1, cnt(3)=3
lfu.get(4);      // 返回 4
                 // cache=[3,4], cnt(4)=2, cnt(3)=3





```
use crate::linked_list::{LinkedList, Node};
use std::{collections::HashMap, ptr::NonNull};


struct LFUCache {
    capacity: u32,// 缓存容量
    min_freq: u32,// 当前最小频率
    key_map: HashMap<i32, NonNull<Node>>,// key -> 节点指针
    count_map: HashMap<u32, LinkedList>,// 频率 -> 该频率的所有节点链表
}

impl LFUCache {
    fn new(capacity: i32) -> Self {
        Self {
            capacity: capacity as _,
            min_freq: 1,
            key_map: HashMap::new(),
            count_map: HashMap::new(),
        }
    }

    fn get(&mut self, key: i32) -> i32 {
        unsafe {
            match self.update(key) {
                Some(node) => {
                    let node = node.as_ref();
                    node.value
                }
                None => -1,
            }
        }
    }

    fn put(&mut self, key: i32, value: i32) {
        unsafe {
            match self.update(key) {
                Some(mut node) => {
                    node.as_mut().value = value;
                }
                None => {
                    self.eliminate();

                    self.min_freq = 1;
                    let new_node =
                        NonNull::new_unchecked(Box::into_raw(Box::new(Node::new(key, value))));
                    self.key_map.insert(key, new_node);
                    match self.count_map.get_mut(&1) {
                        Some(list) => {
                            list.append_last(new_node);
                        }
                        None => {
                            let mut list = LinkedList::new();
                            list.append_last(new_node);
                            self.count_map.insert(1, list);
                        }
                    }
                }
            }
        }
    }

    fn update(&mut self, key: i32) -> Option<NonNull<Node>> {
        unsafe {
            match self.key_map.get(&key) {
                Some(&(mut node)) => {
                    LinkedList::remove(node);

                    {
                        let node = node.as_mut();
                        if self.min_freq == node.freq && self.count_map[&node.freq].is_empty() {
                            self.min_freq += 1;
                            self.count_map.remove(&node.freq);
                        }
                    }

                    node.as_mut().freq += 1;
                    match self.count_map.get_mut(&node.as_ref().freq) {
                        Some(list) => {
                            list.append_last(node);
                        }
                        None => {
                            let mut list = LinkedList::new();
                            list.append_last(node);
                            self.count_map.insert(node.as_ref().freq, list);
                        }
                    }

                    Some(node)
                }
                None => return None,
            }
        }
    }

    fn eliminate(&mut self) {
        if self.key_map.len() < self.capacity as _ {
            return;
        }

        // 从最小频率的链表中取出第一个节点（最久未使用）
        let candidate_node = self
            .count_map
            .get_mut(&self.min_freq)
            .map(|list| list.pop_front().expect("must contains at least one node"))
            .expect("mut contains linked list");

        unsafe {
            // 从key_map中移除并释放内存
            let node = candidate_node.as_ref();
            self.key_map.remove(&node.key);
            drop(Box::from_raw(candidate_node.as_ptr()));
        }
    }
}

mod linked_list {
    use std::{collections::HashSet, ptr::NonNull};

    pub struct Node {
        pub key: i32,
        pub value: i32,
        pub freq: u32,
        prev: Option<NonNull<Node>>,
        next: Option<NonNull<Node>>,
    }

    pub struct LinkedList {
        head: NonNull<Node>,
        tail: NonNull<Node>,
    }

    impl Node {
        pub fn new(key: i32, value: i32) -> Self {
            Self {
                key,
                value,
                freq: 1,
                prev: None,
                next: None,
            }
        }
    }

    impl LinkedList {
        pub fn new() -> Self {
            unsafe {
                let mut head = NonNull::new_unchecked(Box::into_raw(Box::new(Node::new(0, 0))));
                let mut tail = NonNull::new_unchecked(Box::into_raw(Box::new(Node::new(0, 0))));
                {
                    head.as_mut().next = Some(tail);
                    tail.as_mut().prev = Some(head);
                }

                Self { head, tail }
            }
        }

        pub fn append_last(&mut self, mut node: NonNull<Node>) {
            unsafe {
                {
                    let node = node.as_mut();
                    node.next = Some(self.tail);
                    node.prev = self.tail.as_ref().prev;
                }
                self.tail
                    .as_mut()
                    .prev
                    .expect("must at least contains dummy node")
                    .as_mut()
                    .next = Some(node);
                self.tail.as_mut().prev = Some(node);
            }
        }

        pub fn pop_front(&mut self) -> Option<NonNull<Node>> {
            unsafe {
                match self.head.as_mut().next {
                    Some(mut front) => {
                        {
                            let front = front.as_mut();
                            self.head.as_mut().next = front.next;
                            front
                                .next
                                .expect("must at least contains dummy node")
                                .as_mut()
                                .prev = Some(self.head);
                        }
                        Some(front)
                    }
                    None => return None,
                }
            }
        }

        pub fn is_empty(&self) -> bool {
            unsafe {
                self.head
                    .as_ref()
                    .next
                    .expect("must at least contains dummy node")
                    == self.tail
            }
        }

        pub fn remove(mut node: NonNull<Node>) {
            unsafe {
                let node = node.as_mut();
                node.prev.map(|mut prev| prev.as_mut().next = node.next);
                node.next.map(|mut next| next.as_mut().prev = node.prev);
            }
        }

        #[allow(unused)]
        fn check(&self) {
            let mut visited = HashSet::new();

            let mut next = self.head;
            loop {
                if visited.contains(&next) {
                    panic!("")
                }
                visited.insert(next);
                match unsafe { next.as_ref().next } {
                    Some(node) => unsafe {
                        assert_eq!(node.as_ref().prev, Some(next));
                        next = node;
                    },
                    None => return,
                }
            }
        }
    }

    #[cfg(test)]
    mod test {
        use std::ptr::NonNull;

        use crate::linked_list::{LinkedList, Node};

        #[test]
        fn append_test() {
            let mut list = LinkedList::new();
            for i in 0..10 {
                unsafe {
                    list.append_last(NonNull::new_unchecked(Box::into_raw(Box::new(Node::new(
                        i, i,
                    )))));
                }
            }
            list.check();
        }

        #[test]
        fn pop_test() {
            let mut list = LinkedList::new();
            for i in 0..10 {
                unsafe {
                    list.append_last(NonNull::new_unchecked(Box::into_raw(Box::new(Node::new(
                        i, i,
                    )))));
                }
            }

            for i in 0..5 {
                let node = unsafe { list.pop_front().unwrap().as_ref() };

                assert_eq!(node.key, i);
                assert_eq!(node.value, i);
            }

            list.check();
        }
    }
}

```


LFU缓存的核心思想：当缓存达到容量上限时，淘汰使用频率最低的元素。如果多个元素有相同的最低频率，则淘汰最久未使用的那个。


核心算法原理

get 操作流程

调用 update(key) 更新节点的访问频率
返回节点的值，如果不存在返回 -1
put 操作流程

如果key存在：更新节点的值，并调用 update(key) 更新频率
如果key不存在：

调用 eliminate() 淘汰一个元素（如果缓存已满）
创建新节点，频率设为1
将新节点加入频率为1的链表
update 方法（关键逻辑）

这是LFU算法的核心：

从key_map找到节点
从原频率链表中移除该节点
如果原频率链表变空且等于最小频率，更新min_freq
节点频率+1
将节点加入新频率的链表



特点

使用 NonNull<Node> 实现安全的裸指针操作
带有虚拟头尾节点，简化边界处理
提供 O(1) 的头尾操作
链表操作

append_last(): 在尾部添加节点
pop_front(): 移除头部节点
remove(): 从链表中移除指定节点
is_empty(): 检查链表是否为空（排除虚拟节点）
时间复杂度分析

所有操作都是 O(1) 时间复杂度：

get: 哈希查找 + 链表操作
put: 哈希插入/更新 + 链表操作
update: 常数次哈希和链表操作
内存安全

代码大量使用 unsafe 是因为：

需要操作裸指针实现高效的双向链表
使用 NonNull 确保指针不为空
手动管理内存（Box::into_raw 和 Box::from_raw）
