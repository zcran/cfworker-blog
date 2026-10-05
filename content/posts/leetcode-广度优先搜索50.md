---
title: "leetcode-广度优先搜索50"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 装饰树

力扣嘉年华上的 DIY 手工展位准备了一棵缩小版的 二叉 装饰树 root 和灯饰，你需要将灯饰逐一插入装饰树中，要求如下：

· 完成装饰的二叉树根结点与 root 的根结点值相同
· 若一个节点拥有父节点，则在该节点和他的父节点之间插入一个灯饰（即插入一个值为 -1 的节点）。具体地：
    在一个 父节点 x 与其左子节点 y 之间添加 -1 节点， 节点 -1、节点 y 为各自父节点的左子节点，
    在一个 父节点 x 与其右子节点 y 之间添加 -1 节点， 节点 -1、节点 y 为各自父节点的右子节点，

现给定二叉树的根节点 root ，请返回完成装饰后的树的根节点。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    pub fn expand_binary_tree(
        root: Option<Rc<RefCell<TreeNode>>>,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        let node = root?;

        // 取出左右子节点，稍后分别递归处理
        let left = node.borrow_mut().left.take();
        let right = node.borrow_mut().right.take();

        // 左子节点存在时，在中间插入一个值为 -1 的灯饰节点
        if left.is_some() {
            node.borrow_mut().left = Some(Rc::new(RefCell::new(TreeNode {
                val: -1,
                left: Self::expand_binary_tree(left),
                right: None,
            })));
        }

        // 右子节点存在时，在中间插入一个值为 -1 的灯饰节点
        if right.is_some() {
            node.borrow_mut().right = Some(Rc::new(RefCell::new(TreeNode {
                val: -1,
                left: None,
                right: Self::expand_binary_tree(right),
            })));
        }

        Some(node)
    }
}
```
