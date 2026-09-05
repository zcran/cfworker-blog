---
title: "leetcode-二叉树28"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 寻找重复的子树

给你一棵二叉树的根节点 root ，返回所有 重复的子树 。

对于同一类的重复子树，你只需要返回其中任意 一棵 的根结点即可。

如果两棵树具有 相同的结构 和 相同的结点值 ，则认为二者是 重复 的。


```
use std::cell::RefCell;
use std::collections::HashMap;
use std::rc::Rc;

impl Solution {
    /// 返回所有重复子树的根节点（每类只返回一个）
    ///
    /// 后序遍历，用序列化字符串作为子树的唯一标识。
    /// 时间复杂度 O(n²)（字符串拼接），空间复杂度 O(n²)
    pub fn find_duplicate_subtrees(
        root: Option<Rc<RefCell<TreeNode>>>,
    ) -> Vec<Option<Rc<RefCell<TreeNode>>>> {
        let mut ans = Vec::new();
        let mut cnt = HashMap::new();
        Self::dfs(root, &mut cnt, &mut ans);
        ans
    }

    fn dfs(
        node: Option<Rc<RefCell<TreeNode>>>,
        cnt: &mut HashMap<String, i32>,
        ans: &mut Vec<Option<Rc<RefCell<TreeNode>>>>,
    ) -> String {
        let Some(node) = node else { return "#".to_string() };

        let n = node.borrow();
        let left = Self::dfs(n.left.clone(), cnt, ans);
        let right = Self::dfs(n.right.clone(), cnt, ans);

        // 用特殊分隔符避免歧义，如 val=1,left=12 和 val=11,left=2 会冲突
        let key = format!("{}({})({})", n.val, left, right);

        let count = cnt.entry(key.clone()).or_insert(0);
        *count += 1;
        if *count == 2 {
            ans.push(Some(node.clone()));
        }

        key
    }
}
```
