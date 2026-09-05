---
title: "leetcode-二叉树44"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的堂兄弟节点

在二叉树中，根节点位于深度 0 处，每个深度为 k 的节点的子节点位于深度 k+1 处。

如果二叉树的两个节点深度相同，但 父节点不同 ，则它们是一对堂兄弟节点。

我们给出了具有唯一值的二叉树的根节点 root ，以及树中两个不同节点的值 x 和 y 。

只有与值 x 和 y 对应的节点是堂兄弟节点时，才返回 true 。否则，返回 false。


```
use std::cell::RefCell;
use std::collections::VecDeque;
use std::rc::Rc;

impl Solution {
    /// BFS 层序遍历。堂兄弟必须满足：深度相同且父节点不同。
    /// 优化：不需要记录所有节点的深度和父节点，只需在当前层中寻找 x 和 y。
    /// 若同一层中同时找到两者且父节点不同，返回 true；若只找到一个，返回 false。
    pub fn is_cousins(root: Option<Rc<RefCell<TreeNode>>>, x: i32, y: i32) -> bool {
        let Some(root) = root else { return false };
        let mut queue = VecDeque::new();
        queue.push_back((root, 0)); // (node, parent_val), root 的 parent 用 0（与节点值不冲突）

        while !queue.is_empty() {
            let mut found_x = None;
            let mut found_y = None;

            // 处理当前层所有节点
            for _ in 0..queue.len() {
                let (node, parent) = queue.pop_front().unwrap();
                let n = node.borrow();

                if n.val == x {
                    found_x = Some(parent);
                } else if n.val == y {
                    found_y = Some(parent);
                }

                // 提前剪枝：同一父节点的两个子节点不可能是堂兄弟
                if let Some(left) = n.left.as_ref() {
                    queue.push_back((left.clone(), n.val));
                }
                if let Some(right) = n.right.as_ref() {
                    queue.push_back((right.clone(), n.val));
                }
            }

            match (found_x, found_y) {
                (Some(px), Some(py)) => return px != py, // 同层找到两个，判断父节点是否不同
                (None, None) => continue,                // 都没找到，继续下一层
                _ => return false,                        // 只找到一个，深度不同，不是堂兄弟
            }
        }

        false
    }
}
```
