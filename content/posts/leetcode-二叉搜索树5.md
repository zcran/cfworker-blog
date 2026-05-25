---
title: "leetcode-二叉搜索树5"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


##  将有序数组转换为二叉搜索树


给你一个整数数组 nums ，其中元素已经按 升序 排列，请你将其转换为一棵 平衡 二叉搜索树。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 将有序数组转换为高度平衡的二叉搜索树 (BST)
    ///
    /// # 原理
    /// 每次选取中间元素作为根节点，递归构建左子树和右子树。
    /// 由于数组已排序，这样构造的 BST 自然满足左 < 根 < 右，且高度平衡。
    ///
    /// # 复杂度
    /// - 时间：O(n)，每个元素访问一次
    /// - 空间：O(log n) 递归栈深度 + O(n) 用于存储树节点（必须的）
    pub fn sorted_array_to_bst(nums: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        // 递归构建闭包：接收一个切片，返回子树根节点
        fn build(slice: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
            match slice.len() {
                0 => None,
                n => {
                    let mid = n / 2;               // 中间索引（左中）
                    let val = slice[mid];
                    Some(Rc::new(RefCell::new(TreeNode {
                        val,
                        left: build(&slice[..mid]),    // 左半部分（不包括 mid）
                        right: build(&slice[mid + 1..]), // 右半部分
                    })))
                }
            }
        }
        build(&nums)
    }
}
```
