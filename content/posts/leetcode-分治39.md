---
title: "leetcode-分治39"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


##  推理二叉树

某二叉树的先序遍历结果记录于整数数组 preorder，它的中序遍历结果记录于整数数组 inorder。请根据 preorder 和 inorder 的提示构造出这棵二叉树并返回其根节点。



注意：preorder 和 inorder 中均不含重复数字。


```
use std::cell::RefCell;
use std::collections::{HashMap, VecDeque};
use std::rc::Rc;

// Definition for a binary tree node.
#[derive(Debug, PartialEq, Eq, Clone)]
pub struct TreeNode {
    pub val: i32,
    pub left: Option<Rc<RefCell<TreeNode>>>,
    pub right: Option<Rc<RefCell<TreeNode>>>,
}

impl TreeNode {
    #[inline]
    pub fn new(val: i32) -> Self {
        TreeNode {
            val,
            left: None,
            right: None,
        }
    }
}

// 序列化 trait
pub trait Serialize {
    fn serialize(&self) -> String;
}

// 为 Option<Rc<RefCell<TreeNode>>> 实现 Serialize
impl Serialize for Option<Rc<RefCell<TreeNode>>> {
    fn serialize(&self) -> String {
        match self {
            None => {
                // 空树返回空数组
                "[]".to_string()
            }
            Some(node) => {
                let mut result = Vec::new();
                let mut queue = VecDeque::new();
                queue.push_back(Some(node.clone()));

                while let Some(node_opt) = queue.pop_front() {
                    match node_opt {
                        None => {
                            result.push("null".to_string());
                        }
                        Some(n) => {
                            let n_ref = n.borrow();
                            result.push(n_ref.val.to_string());
                            queue.push_back(n_ref.left.clone());
                            queue.push_back(n_ref.right.clone());
                        }
                    }
                }

                // 移除末尾的 null
                while let Some(last) = result.last() {
                    if last == "null" {
                        result.pop();
                    } else {
                        break;
                    }
                }

                // 如果结果为空，返回 []
                if result.is_empty() {
                    "[]".to_string()
                } else {
                    format!("[{}]", result.join(","))
                }
            }
        }
    }
}


impl Solution {
    /// 根据前序遍历和中序遍历构造二叉树
    ///
    /// # 参数
    /// - `preorder`: 前序遍历数组
    /// - `inorder`: 中序遍历数组
    ///
    /// # 返回
    /// 构造的二叉树的根节点
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// let preorder = vec![3, 9, 20, 15, 7];
    /// let inorder = vec![9, 3, 15, 20, 7];
    /// let root = Solution::deduce_tree(preorder, inorder);
    /// assert_eq!(root.serialize(), "[3,9,20,null,null,15,7]");
    /// ```
    pub fn deduce_tree(preorder: Vec<i32>, inorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        let len = preorder.len();
        if len == 0 {
            return None;
        }

        // 构建中序遍历的值到索引的映射，加速查找
        let mut index_map = HashMap::with_capacity(len);
        for (idx, &val) in inorder.iter().enumerate() {
            index_map.insert(val, idx);
        }

        // 递归构建
        Self::build(&preorder, &inorder, &index_map, 0, 0, len)
    }

    /// 递归构建二叉树的辅助函数
    fn build(
        preorder: &[i32],
        inorder: &[i32],
        index_map: &HashMap<i32, usize>,
        pre_start: usize,
        in_start: usize,
        len: usize,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        if len == 0 {
            return None;
        }

        // 根节点值：前序遍历的第一个元素
        let root_val = preorder[pre_start];

        // 在中序遍历中找到根节点的位置
        let root_in_idx = *index_map.get(&root_val).unwrap();

        // 左子树的大小
        let left_len = root_in_idx - in_start;

        // 创建根节点
        let root = Rc::new(RefCell::new(TreeNode::new(root_val)));

        // 构建左子树
        root.borrow_mut().left = Self::build(
            preorder,
            inorder,
            index_map,
            pre_start + 1,
            in_start,
            left_len,
        );

        // 构建右子树
        root.borrow_mut().right = Self::build(
            preorder,
            inorder,
            index_map,
            pre_start + left_len + 1,
            root_in_idx + 1,
            len - left_len - 1,
        );

        Some(root)
    }
}
```
