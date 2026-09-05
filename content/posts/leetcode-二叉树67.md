---
title: "leetcode-二叉树67"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 根据描述创建二叉树

给你一个二维整数数组 descriptions ，其中 descriptions[i] = [parenti, childi, isLefti] 表示 parenti 是 childi 在 二叉树 中的 父节点，二叉树中各节点的值 互不相同 。此外：

如果 isLefti == 1 ，那么 childi 就是 parenti 的左子节点。
如果 isLefti == 0 ，那么 childi 就是 parenti 的右子节点。
请你根据 descriptions 的描述来构造二叉树并返回其 根节点 。

测试用例会保证可以构造出 有效 的二叉树。


```
use std::cell::RefCell;
use std::collections::HashMap;
use std::rc::Rc;

impl Solution {
    pub fn create_binary_tree(descriptions: Vec<Vec<i32>>) -> Option<Rc<RefCell<TreeNode>>> {
        let mut children: HashMap<i32, (Option<i32>, Option<i32>)> = HashMap::new();
        let mut is_child: HashMap<i32, bool> = HashMap::new();

        for d in descriptions {
            let (p, c, left) = (d[0], d[1], d[2] == 1);
            is_child.insert(c, true);
            is_child.entry(p).or_insert(false);

            let entry = children.entry(p).or_insert((None, None));
            if left {
                entry.0 = Some(c);
            } else {
                entry.1 = Some(c);
            }
        }

        let root_val = *is_child.iter()
            .find(|&(_, v)| !v)
            .map(|(k, _)| k)
            .unwrap();

        Self::build(root_val, &children)
    }

    fn build(val: i32, children: &HashMap<i32, (Option<i32>, Option<i32>)>) -> Option<Rc<RefCell<TreeNode>>> {
        let node = Rc::new(RefCell::new(TreeNode::new(val)));
        if let Some(&(l, r)) = children.get(&val) {
            node.borrow_mut().left = l.and_then(|v| Self::build(v, children));
            node.borrow_mut().right = r.and_then(|v| Self::build(v, children));
        }
        Some(node)
    }
}
```
