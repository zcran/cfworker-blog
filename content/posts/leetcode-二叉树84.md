---
title: "leetcode-二叉树84"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 力扣泡泡龙

欢迎各位勇者来到力扣城，本次试炼主题为「力扣泡泡龙」。

游戏初始状态的泡泡形如二叉树 root，每个节点值对应了该泡泡的分值。勇者最多可以击破一个节点泡泡，要求满足：

被击破的节点泡泡 至多 只有一个子节点泡泡
当被击破的节点泡泡有子节点泡泡时，则子节点泡泡将取代被击破泡泡的位置
    注：即整棵子树泡泡上移

请问在击破一个节点泡泡操作或无击破操作后，二叉泡泡树的最大「层和」是多少。

注意：

「层和」为同一高度的所有节点的分值之和


```
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug, Clone)]
struct LayerInfo {
    sum: i32,
    count: i32,
}

#[derive(Debug)]
struct SubtreeDelta {
    root_val: i32,
    max_depth: usize,
    layers: Vec<LayerInfo>,
    pending_depth: usize,
}

impl Solution {
    pub fn get_max_layer_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut layer_sum: Vec<LayerInfo> = Vec::new();
        Self::calc_layer_sum(root.as_ref().unwrap(), 0, &mut layer_sum);

        let mut best_delta = vec![0; layer_sum.len()];
        Self::calc_delta(root.as_ref().unwrap(), 0, &layer_sum, &mut best_delta);

        layer_sum
            .iter()
            .zip(&best_delta)
            .map(|(info, delta)| info.sum + delta)
            .max()
            .unwrap()
    }

    fn calc_layer_sum(node: &Rc<RefCell<TreeNode>>, depth: usize, layer_sum: &mut Vec<LayerInfo>) {
        let node = node.borrow();
        if depth == layer_sum.len() {
            layer_sum.push(LayerInfo { sum: node.val, count: 1 });
        } else {
            layer_sum[depth].sum += node.val;
            layer_sum[depth].count += 1;
        }
        if let Some(ref left) = node.left {
            Self::calc_layer_sum(left, depth + 1, layer_sum);
        }
        if let Some(ref right) = node.right {
            Self::calc_layer_sum(right, depth + 1, layer_sum);
        }
    }

    fn calc_delta(
        node: &Rc<RefCell<TreeNode>>,
        depth: usize,
        layer_sum: &[LayerInfo],
        best_delta: &mut [i32],
    ) -> SubtreeDelta {
        let node = node.borrow();
        let val = node.val;

        match (node.left.as_ref(), node.right.as_ref()) {
            (Some(left), Some(right)) => {
                let mut left_delta = Self::calc_delta(left, depth + 1, layer_sum, best_delta);
                let mut right_delta = Self::calc_delta(right, depth + 1, layer_sum, best_delta);
                if left_delta.max_depth < right_delta.max_depth {
                    std::mem::swap(&mut left_delta, &mut right_delta);
                }
                left_delta.layers.push(LayerInfo {
                    sum: -val + left_delta.root_val + right_delta.root_val,
                    count: 1,
                });
                let right_max = right_delta.max_depth;
                for d in (depth + 1)..=right_max {
                    let li = left_delta.max_depth - d;
                    let ri = right_max - d;
                    left_delta.layers[li].sum += right_delta.layers[ri].sum;
                    left_delta.layers[li].count += right_delta.layers[ri].count;
                }
                left_delta.pending_depth = left_delta.pending_depth.max(right_max + 1);
                left_delta.root_val = val;
                left_delta
            }
            (Some(left), None) => {
                let mut ans = Self::calc_delta(left, depth + 1, layer_sum, best_delta);
                ans.layers.push(LayerInfo {
                    sum: -val + ans.root_val,
                    count: 1,
                });
                Self::apply_delta(depth, &ans, layer_sum, best_delta);
                ans.pending_depth = depth;
                ans.root_val = val;
                ans
            }
            (None, Some(right)) => {
                let mut ans = Self::calc_delta(right, depth + 1, layer_sum, best_delta);
                ans.layers.push(LayerInfo {
                    sum: -val + ans.root_val,
                    count: 1,
                });
                Self::apply_delta(depth, &ans, layer_sum, best_delta);
                ans.pending_depth = depth;
                ans.root_val = val;
                ans
            }
            (None, None) => {
                let delta_val = -val;
                // 叶子节点直接更新当前深度（pending_depth == depth 会导致 apply_delta 循环为空）
                if depth + 1 != layer_sum.len() || layer_sum[depth].count > 1 {
                    best_delta[depth] = best_delta[depth].max(delta_val);
                }
                SubtreeDelta {
                    root_val: val,
                    max_depth: depth,
                    pending_depth: depth,
                    layers: vec![LayerInfo { sum: delta_val, count: 1 }],
                }
            }
        }
    }

    fn apply_delta(
        depth: usize,
        delta: &SubtreeDelta,
        layer_sum: &[LayerInfo],
        best_delta: &mut [i32],
    ) {
        for d in depth..delta.pending_depth {
            let idx = delta.max_depth - d;
            if d + 1 != layer_sum.len() || layer_sum[d].count > delta.layers[idx].count {
                best_delta[d] = best_delta[d].max(delta.layers[idx].sum);
            }
        }
    }
}
```
