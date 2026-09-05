---
title: "leetcode-二叉树90"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的序列化与反序列化

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

struct Codec;

impl Codec {
    fn new() -> Self {
        Codec
    }

    /// 将二叉树序列化为层序遍历数组，`None` 表示空节点。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(w)。
    fn serialize(&self, root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Option<i32>> {
        let mut ans = Vec::new();
        let mut queue = VecDeque::new();

        if let Some(node) = root {
            queue.push_back(Some(node));
        }

        while let Some(node) = queue.pop_front() {
            if let Some(n) = node {
                let n = n.borrow();
                ans.push(Some(n.val));
                queue.push_back(n.left.clone());
                queue.push_back(n.right.clone());
            } else {
                ans.push(None);
            }
        }

        ans
    }

    /// 从层序遍历数组反序列化为二叉树。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(w)。
    fn deserialize(&self, data: Vec<Option<i32>>) -> Option<Rc<RefCell<TreeNode>>> {
        if data.is_empty() {
            return None;
        }

        let root = Rc::new(RefCell::new(TreeNode::new(data[0]?)));
        let mut queue = VecDeque::from([root.clone()]);
        let mut iter = data[1..].iter();

        while let Some(parent) = queue.pop_front() {
            // 每轮取两个值作为当前父节点的左右子节点
            if let Some(&v) = iter.next() {
                if let Some(val) = v {
                    let left = Rc::new(RefCell::new(TreeNode::new(val)));
                    parent.borrow_mut().left = Some(left.clone());
                    queue.push_back(left);
                }
            }
            if let Some(&v) = iter.next() {
                if let Some(val) = v {
                    let right = Rc::new(RefCell::new(TreeNode::new(val)));
                    parent.borrow_mut().right = Some(right.clone());
                    queue.push_back(right);
                }
            }
        }

        Some(root)
    }
}
```


"LeetCode 测试框架会调用 Codec::new() 来创建实例，因此必须保留 new() 方法。单元结构体 struct Codec; 的 new() 返回 Codec 即可。"
