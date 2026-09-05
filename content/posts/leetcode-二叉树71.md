---
title: "leetcode-二叉树71"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 感染二叉树需要的总时间

给你一棵二叉树的根节点 root ，二叉树中节点的值 互不相同 。另给你一个整数 start 。在第 0 分钟，感染 将会从值为 start 的节点开始爆发。

每分钟，如果节点满足以下全部条件，就会被感染：

节点此前还没有感染。
节点与一个已感染节点相邻。

返回感染整棵树需要的分钟数。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::{HashMap, VecDeque};

impl Solution {
    pub fn amount_of_time(root: Option<Rc<RefCell<TreeNode>>>, start: i32) -> i32 {
        let mut g: HashMap<i32, Vec<i32>> = HashMap::new();
        Self::build(&root, &mut g);

        let mut q = VecDeque::from([(start, 0)]);
        let mut vis = HashMap::with_capacity(g.len());
        vis.insert(start, true);

        let mut ans = 0;
        while let Some((u, t)) = q.pop_front() {
            ans = t;
            for &v in g.get(&u).unwrap_or(&Vec::new()) {
                if !vis.contains_key(&v) {
                    vis.insert(v, true);
                    q.push_back((v, t + 1));
                }
            }
        }
        ans
    }

    /// 将二叉树转为无向图：每个节点与其父节点、子节点相连
    fn build(node: &Option<Rc<RefCell<TreeNode>>>, g: &mut HashMap<i32, Vec<i32>>) {
        let Some(n) = node else { return };
        let n = n.borrow();

        if let Some(ref l) = n.left {
            let lv = l.borrow().val;
            g.entry(n.val).or_default().push(lv);
            g.entry(lv).or_default().push(n.val);
            Self::build(&n.left, g);
        }
        if let Some(ref r) = n.right {
            let rv = r.borrow().val;
            g.entry(n.val).or_default().push(rv);
            g.entry(rv).or_default().push(n.val);
            Self::build(&n.right, g);
        }
    }
}
```
