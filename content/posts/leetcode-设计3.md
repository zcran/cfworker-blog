---
title: "leetcode-设计3"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 二叉树的序列化与反序列化

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。


```
use std::cell::RefCell;
use std::rc::Rc;

// Definition for a binary tree node.
// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//   pub val: i32,
//   pub left: Option<Rc<RefCell<TreeNode>>>,
//   pub right: Option<Rc<RefCell<TreeNode>>>,
// }
// impl TreeNode {
//   #[inline]
//   pub fn new(val: i32) -> Self {
//     TreeNode {
//       val,
//       left: None,
//       right: None,
//     }
//   }
// }

struct Codec;

impl Codec {
    /// 创建一个新的编码器
    fn new() -> Self {
        Codec
    }

    /// 序列化二叉树为字符串
    ///
    /// 使用前序遍历，将空节点编码为特殊标记，
    /// 每个节点的值占 4 字节（i32 的二进制表示）
    fn serialize(&self, root: Option<Rc<RefCell<TreeNode>>>) -> String {
        let mut bytes = Vec::new();
        self.serialize_helper(&root, &mut bytes);
        // 安全：字节序列是有效的 UTF-8（每个 i32 转为 4 字节）
        unsafe { String::from_utf8_unchecked(bytes) }
    }

    /// 递归辅助函数：前序遍历序列化
    fn serialize_helper(&self, node: &Option<Rc<RefCell<TreeNode>>>, bytes: &mut Vec<u8>) {
        match node {
            Some(n) => {
                let n = n.borrow();
                // 写入当前节点值（4 字节大端序）
                bytes.extend_from_slice(&n.val.to_be_bytes());
                // 递归序列化左右子树
                self.serialize_helper(&n.left, bytes);
                self.serialize_helper(&n.right, bytes);
            }
            None => {
                // 使用 i32::MAX 作为空节点标记（假设树中不包含此值）
                bytes.extend_from_slice(&i32::MAX.to_be_bytes());
            }
        }
    }

    /// 反序列化字符串为二叉树
    fn deserialize(&self, data: String) -> Option<Rc<RefCell<TreeNode>>> {
        // 将字符串转为字节迭代器，每次读取 4 字节作为 i32
        let bytes = data.into_bytes();
        let mut iter = bytes.chunks_exact(4).map(|chunk| {
            i32::from_be_bytes(chunk.try_into().unwrap())
        });

        self.deserialize_helper(&mut iter)
    }

    /// 递归辅助函数：前序遍历反序列化
    fn deserialize_helper(&self, iter: &mut dyn Iterator<Item = i32>) -> Option<Rc<RefCell<TreeNode>>> {
        let val = iter.next().expect("Invalid serialized data");

        if val == i32::MAX {
            None // 空节点标记
        } else {
            // 递归构建左右子树
            let left = self.deserialize_helper(iter);
            let right = self.deserialize_helper(iter);

            Some(Rc::new(RefCell::new(TreeNode {
                val,
                left,
                right,
            })))
        }
    }
}
```
