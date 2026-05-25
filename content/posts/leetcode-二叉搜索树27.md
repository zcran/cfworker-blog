---
title: "leetcode-二叉搜索树27"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 合并多棵二叉搜索树

给你 n 个 二叉搜索树的根节点 ，存储在数组 trees 中（下标从 0 开始），对应 n 棵不同的二叉搜索树。trees 中的每棵二叉搜索树 最多有 3 个节点 ，且不存在值相同的两个根节点。在一步操作中，将会完成下述步骤：

选择两个 不同的 下标 i 和 j ，要求满足在 trees[i] 中的某个 叶节点 的值等于 trees[j] 的 根节点的值 。
用 trees[j] 替换 trees[i] 中的那个叶节点。
从 trees 中移除 trees[j] 。
如果在执行 n - 1 次操作后，能形成一棵有效的二叉搜索树，则返回结果二叉树的 根节点 ；如果无法构造一棵有效的二叉搜索树，返回 null 。

二叉搜索树是一种二叉树，且树中每个节点均满足下述属性：

任意节点的左子树中的值都 严格小于 此节点的值。
任意节点的右子树中的值都 严格大于 此节点的值。
叶节点是不含子节点的节点。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashMap;

impl Solution {
    pub fn can_merge(trees: Vec<Option<Rc<RefCell<TreeNode>>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let n = trees.len();

        // 构建 root_map 和 leaf_map
        let (root_map, leaf_map) = trees.iter().enumerate().fold(
            (HashMap::new(), HashMap::new()),
            |(mut root_map, mut leaf_map), (i, tree_opt)| {
                let tree = tree_opt.as_ref().unwrap();
                let node = tree.borrow();
                root_map.insert(node.val, (i, Rc::clone(tree)));

                if let Some(left) = &node.left {
                    leaf_map.insert(left.borrow().val, (i, Rc::clone(left)));
                }
                if let Some(right) = &node.right {
                    leaf_map.insert(right.borrow().val, (i, Rc::clone(right)));
                }
                (root_map, leaf_map)
            },
        );

        // 需要连接的值（既是根值又是叶子值）
        let duplicates: Vec<_> = root_map
            .keys()
            .filter(|val| leaf_map.contains_key(val))
            .collect();

        if duplicates.len() != n - 1 {
            return None;
        }

        // 并查集
        let mut parent: Vec<usize> = (0..n).collect();
        let find = |parent: &mut Vec<usize>, mut x: usize| -> usize {
            while x != parent[x] {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        };
        let mut union = |parent: &mut Vec<usize>, a: usize, b: usize| -> bool {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                false
            } else {
                parent[ra] = rb;
                true
            }
        };

        // 执行合并
        for &val in &duplicates {
            // 从 HashMap 中获取引用，不解引用
            let (root_idx, root_rc_ref) = root_map.get(&val)?;
            let (leaf_idx, leaf_rc_ref) = leaf_map.get(&val)?;

            // 克隆 Rc（增加引用计数），解引用 usize（Copy）
            let root_rc = Rc::clone(root_rc_ref);
            let leaf_rc = Rc::clone(leaf_rc_ref);
            let root_idx = *root_idx;
            let leaf_idx = *leaf_idx;

            if !union(&mut parent, root_idx, leaf_idx) {
                return None;
            }

            let mut leaf_mut = leaf_rc.borrow_mut();
            let root_ref = root_rc.borrow();
            leaf_mut.left = root_ref.left.clone();
            leaf_mut.right = root_ref.right.clone();
        }

        // 找到最终根索引
        let root_idx = find(&mut parent, 0);
        let final_root = trees[root_idx].as_ref()?;

        // 验证 BST（纯函数）
        fn is_bst(node: &Option<Rc<RefCell<TreeNode>>>, min: i32, max: i32) -> bool {
            match node {
                None => true,
                Some(rc) => {
                    let n = rc.borrow();
                    let val = n.val;
                    min < val && val < max
                        && is_bst(&n.left, min, val)
                        && is_bst(&n.right, val, max)
                }
            }
        }

        is_bst(&Some(Rc::clone(final_root)), 0, 50001).then(|| Rc::clone(final_root))
    }
}
```
