---
title: "leetcode-二叉树40"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 监控二叉树

给定一个二叉树，我们在树的节点上安装摄像头。

节点上的每个摄影头都可以监视其父对象、自身及其直接子对象。

计算监控树的所有节点所需的最小摄像头数量。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回监控整棵树所需的最小摄像头数
    ///
    /// 树形 DP，每个节点返回三种状态的最小摄像头数：
    /// - place: 当前节点放摄像头
    /// - by_parent: 当前节点被父节点覆盖（当前不放）
    /// - by_child: 当前节点被子节点覆盖（当前不放）
    ///
    /// 空节点：放摄像头不可能(inf)；被父/子覆盖都无需代价(0)
    /// 时间复杂度 O(n)，空间复杂度 O(h)
    pub fn min_camera_cover(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let (place, _, by_child) = Self::dfs(root);
        place.min(by_child)
    }

    fn dfs(node: Option<Rc<RefCell<TreeNode>>>) -> (i32, i32, i32) {
        let Some(node) = node else {
            return (i32::MAX / 2, 0, 0);
        };

        let n = node.borrow();
        let (l_place, l_parent, l_child) = Self::dfs(n.left.clone());
        let (r_place, r_parent, r_child) = Self::dfs(n.right.clone());

        // 当前放摄像头：子树可以被当前节点覆盖(by_parent)，或子树自己放(place)
        let place = l_parent.min(l_place) + r_parent.min(r_place) + 1;

        // 当前被父覆盖：子树必须被覆盖（自己放 place，或被自己的子节点覆盖 by_child）
        let by_parent = l_place.min(l_child) + r_place.min(r_child);

        // 当前被子节点覆盖：左右子树至少一个放摄像头
        let by_child = (l_place + r_child)
            .min(l_child + r_place)
            .min(l_place + r_place);

        (place, by_parent, by_child)
    }
}
```
