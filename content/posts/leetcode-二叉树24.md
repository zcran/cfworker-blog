---
title: "leetcode-二叉树24"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 根据二叉树创建字符串

给你二叉树的根节点 root ，请你采用前序遍历的方式，将二叉树转化为一个由括号和整数组成的字符串，返回构造出的字符串。

空节点使用一对空括号对 "()" 表示，转化后需要省略所有不影响字符串与原始二叉树之间的一对一映射关系的空括号对。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 前序遍历二叉树，转为括号表示的字符串
    ///
    /// 省略规则：
    /// - 左右都空 → 不写括号
    /// - 左空右不空 → 左必须写 ()，右正常写
    /// - 左不空右空 → 只写左，右省略
    /// - 左右都不空 → 都写
    pub fn tree2str(root: Option<Rc<RefCell<TreeNode>>>) -> String {
        let Some(node) = root else { return String::new() };

        let node = node.borrow();
        let val = node.val.to_string();
        let left = Self::tree2str(node.left.clone());
        let right = Self::tree2str(node.right.clone());

        match (left.is_empty(), right.is_empty()) {
            (true, true) => val,
            (true, false) => format!("{}()({})", val, right),
            (false, true) => format!("{}({})", val, left),
            (false, false) => format!("{}({})({})", val, left, right),
        }
    }
}
```
