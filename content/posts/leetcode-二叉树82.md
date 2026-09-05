---
title: "leetcode-二叉树82"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 导航装置

小扣参加的秋日市集景区共有 N 个景点，景点编号为 1~N。景点内设有 N−1 条双向道路，使所有景点形成了一个二叉树结构，根结点记为 root，景点编号即为节点值。

由于秋日市集景区的结构特殊，游客很容易迷路，主办方决定在景区的若干个景点设置导航装置，按照所在景点编号升序排列后定义装置编号为 1 ~ M。导航装置向游客发送数据，数据内容为列表 \[游客与装置 1 的相对距离,游客与装置 2 的相对距离,...,游客与装置 M 的相对距离\]。由于游客根据导航装置发送的信息来确认位置，因此主办方需保证游客在每个景点接收的数据信息皆不相同。请返回主办方最少需要设置多少个导航装置。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 求最少导航装置数，使得每个景点接收到的距离列表唯一
    ///
    /// 核心观察：兄弟节点（同一父节点的子节点）必须被区分。
    /// 若某节点有两个子树，且两子树内部都无需装置即可区分，
    /// 则该节点必须放置装置来区分左右分支。
    pub fn navigation(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut res = 0;
        let mut need_root = 1;

        // 先绑定 Rc，再借用，避免临时值被提前释放
        let root_rc = root.unwrap();
        let (left_root, right_root) = {
            let mut r = root_rc.borrow_mut();
            (r.left.take(), r.right.take())
        };

        let left = Self::dfs(left_root, &mut res, &mut need_root);
        let right = Self::dfs(right_root, &mut res, &mut need_root);

        if left == 1 && right == 1 { res } else { res + need_root }
    }

    /// 返回该子树的状态：
    /// - 0：子树是"简单"的，需要父节点帮忙区分
    /// - 1：子树已内部解决
    fn dfs(node: Option<Rc<RefCell<TreeNode>>>, res: &mut i32, need_root: &mut i32) -> i32 {
        let n = match node {
            Some(n) => n,
            None => return 0,
        };

        let n_rc = n;
        let mut n = n_rc.borrow_mut();
        let has_left = n.left.is_some();
        let has_right = n.right.is_some();

        let left = Self::dfs(n.left.take(), res, need_root);
        let right = Self::dfs(n.right.take(), res, need_root);

        if has_left && has_right {
            if left == 0 && right == 0 {
                *res += 1;
            }
            *need_root = if left == 1 && right == 1 { 0 } else { 1 };
            1
        } else {
            left | right
        }
    }
}
```
