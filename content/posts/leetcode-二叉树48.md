---
title: "leetcode-二叉树48"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 从先序遍历还原二叉树

我们从二叉树的根节点 root 开始进行深度优先搜索。

在遍历中的每个节点处，我们输出 D 条短划线（其中 D 是该节点的深度），然后输出该节点的值。（如果节点的深度为 D，则其直接子节点的深度为 D + 1。根节点的深度为 0）。

如果节点只有一个子节点，那么保证该子节点为左子节点。

给出遍历输出 S，还原树并返回其根节点 root。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 从先序遍历字符串还原二叉树。
    /// 字符串格式：D 个 '-' 后跟节点值，D 为节点深度。
    /// 用栈维护当前路径上的节点，每解析一个新节点时：
    /// 1. 弹出栈中深度 >= 当前深度的节点（它们已不再是后续节点的祖先）
    /// 2. 栈顶即为父节点，将新节点挂为左或右子节点
    pub fn recover_from_preorder(s: String) -> Option<Rc<RefCell<TreeNode>>> {
        let mut stack: Vec<(i32, Rc<RefCell<TreeNode>>)> = Vec::new();
        let mut chars = s.chars().peekable();

        while chars.peek().is_some() {
            // 统计连续 '-' 得到深度
            let mut depth = 0;
            while chars.peek() == Some(&'-') {
                depth += 1;
                chars.next();
            }

            // 读取连续数字得到节点值
            let mut val = 0;
            while let Some(&c) = chars.peek() {
                if let Some(d) = c.to_digit(10) {
                    val = val * 10 + d as i32;
                    chars.next();
                } else {
                    break;
                }
            }

            let node = Rc::new(RefCell::new(TreeNode::new(val)));

            // 弹出非祖先节点，栈顶即为父节点
            while let Some(&(d, _)) = stack.last() {
                if d >= depth {
                    stack.pop();
                } else {
                    break;
                }
            }

            // 挂到父节点下
            if let Some(&(_, ref parent)) = stack.last() {
                let mut p = parent.borrow_mut();
                if p.left.is_none() {
                    p.left = Some(node.clone());
                } else {
                    p.right = Some(node.clone());
                }
            }

            stack.push((depth, node));
        }

        // 根节点深度为 0，是栈中第一个元素
        stack.into_iter().next().map(|(_, root)| root)
    }
}
```
