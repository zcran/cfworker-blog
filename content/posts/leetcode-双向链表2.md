---
title: "Leetcode 双向链表2"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---

## 扁平化多级双向链表

你会得到一个双链表，其中包含的节点有一个下一个指针、一个前一个指针和一个额外的 子指针 。这个子指针可能指向一个单独的双向链表，也包含这些特殊的节点。这些子列表可以有一个或多个自己的子列表，以此类推，以生成如下面的示例所示的 多层数据结构 。

给定链表的头节点 head ，将链表 扁平化 ，以便所有节点都出现在单层双链表中。让 curr 是一个带有子列表的节点。子列表中的节点应该出现在扁平化列表中的 curr 之后 和 curr.next 之前 。

返回 扁平列表的 head 。列表中的节点必须将其 所有 子指针设置为 null 。

以 示例 1 为例：

 1---2---3---4---5---6--NULL
         |
         7---8---9---10--NULL
             |
             11--12--NULL
序列化其中的每一级之后：

[1,2,3,4,5,6,null]
[7,8,9,10,null]
[11,12,null]
为了将每一级都序列化到一起，我们需要每一级中添加值为 null 的元素，以表示没有节点连接到上一级的上级节点。

[1,2,3,4,5,6,null]
[null,null,7,8,9,10,null]
[null,11,12,null]
合并所有序列化结果，并去除末尾的 null 。

[1,2,3,4,5,6,null,null,null,7,8,9,10,null,null,11,12]


C++解法：
```
class Solution {
public:
    Node* flatten(Node* head) {
        auto dfs = [](auto &&dfs, Node *head, Node * rest) -> Node*{
            if (!head) return rest;
            head->next = dfs(dfs, head->child, dfs(dfs, head->next, rest));
            if (head->next) head->next->prev = head;
            head->child = nullptr;
            return head;
        };
        return dfs(dfs, head, nullptr);
    }
};
```


Rust解法：
```
1.Rc<RefCell>：共享所有权+内部可变性，安全管理链表节点的引用与修改；

2.Option枚举：优雅处理空指针场景，避免野指针；

3.take()方法：转移child字段所有权，确保扁平化后原节点无残留子节点；

4.borrow_mut()/drop()：严格遵循借用规则，避免可变借用冲突；

5.模式匹配：通过if let简化Option类型的解构与判断。

#[derive(Debug, Clone, PartialEq, Eq)]

pub struct Node {

pub val: i32,

pub prev: Option<Rc<RefCell<Node>>>,

pub next: Option<Rc<RefCell<Node>>>,

pub child: Option<Rc<RefCell<Node>>>,

}

impl Node {

#[inline]

pub fn new(val: i32) -> Self {

Node {

val,

prev: None,

next: None,

child: None,

}

}

}

use std::rc::Rc;

use std::cell::RefCell;

struct Solution;

impl Solution {

pub fn flatten(head: Option<Rc<RefCell<Node>>>) -> Option<Rc<RefCell<Node>>> {

if let Some(head) = head.clone() {

Self::dfs(head);

}

head

}

fn dfs(node: Rc<RefCell<Node>>) -> Rc<RefCell<Node>> {

let mut node_mut = node.borrow_mut();


if let Some(child) = node_mut.child.take() {

let next = node_mut.next.clone();

let child_tail = Self::dfs(child.clone());

node_mut.next = Some(child.clone());

child.borrow_mut().prev = Some(node.clone());

child_tail.borrow_mut().next = next.clone();

if let Some(next) = next {

next.borrow_mut().prev = Some(child_tail.clone());

}

if let Some(next) = next {

drop(node_mut);

return Self::dfs(next);

} else {

return child_tail;

}

}

if let Some(next) = node_mut.next.clone() {

drop(node_mut);

Self::dfs(next)

} else {

drop(node_mut);

node

}

}

}

```
