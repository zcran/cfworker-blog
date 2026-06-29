---
title: "leetcode-线段树13"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 使数组元素相等的最小操作次数

给你一个整数数组 nums 和一个整数 k。

在一次操作中，你可以恰好将 nums 中的某个元素 增加或减少 k 。

还给定一个二维整数数组 queries，其中每个 queries[i] = [li, ri]。

对于每个查询，找到将 子数组 nums[li..ri] 中的 所有 元素变为相等所需的 最小 操作次数。如果无法实现，返回 -1。

返回一个数组 ans，其中 ans[i] 是第 i 个查询的答案。

子数组 是数组中一个连续、非空 的元素序列。


```
use std::collections::HashMap;

/// 持久化线段树节点
#[derive(Clone)]
struct Node {
    left: usize,   // 左子节点索引
    right: usize,  // 右子节点索引
    count: i32,    // 区间内元素个数
    sum: i64,      // 区间内元素和
}

impl Node {
    fn new() -> Self {
        Node {
            left: 0,
            right: 0,
            count: 0,
            sum: 0,
        }
    }
}

impl Solution {
    /// 计算将子数组所有元素变为相等的最小操作次数
    ///
    /// 思路：
    /// 1. 将每个数表示为 q*k + r 的形式，其中 r = num % k
    /// 2. 如果区间内所有数的余数不同，则无法通过加减 k 变为相等，返回 -1
    /// 3. 否则问题转化为：在区间 [l, r] 中找中位数，计算所有数变为中位数的代价
    /// 4. 使用持久化线段树维护 q 值的频次和总和，支持区间查询和中位数查找
    pub fn min_operations(nums: Vec<i32>, k: i32, queries: Vec<Vec<i32>>) -> Vec<i64> {
        let n = nums.len();
        let k = k as i64;

        // ---------- 预处理：计算 q = num / k 和 r = num % k ----------
        let mut q_vals = vec![0i64; n];
        let mut rems = vec![0i32; n];
        for i in 0..n {
            let num = nums[i] as i64;
            // 处理负数：确保余数在 [0, k) 范围内
            let mut r = num % k;
            if r < 0 {
                r += k;
            }
            rems[i] = r as i32;
            q_vals[i] = (num - r) / k;
        }

        // ---------- 构建线段树：查询区间内余数的最大值和最小值 ----------
        // 用于快速判断区间内所有余数是否相等
        let sz = 4 * n;
        let mut min_rem = vec![0i32; sz];
        let mut max_rem = vec![0i32; sz];

        build_segment_tree(1, 0, n - 1, &rems, &mut min_rem, &mut max_rem);

        // ---------- 离散化 q 值 ----------
        let mut sorted_q = q_vals.clone();
        sorted_q.sort_unstable();
        sorted_q.dedup();
        let m = sorted_q.len();

        // 建立值到索引的映射
        let mut value_to_idx = HashMap::with_capacity(m);
        for (i, &val) in sorted_q.iter().enumerate() {
            value_to_idx.insert(val, i);
        }

        // ---------- 构建持久化线段树 ----------
        // nodes[0] 为空树
        let mut nodes = vec![Node::new()];
        let mut roots = vec![0usize; n + 1];

        for i in 0..n {
            let pos = value_to_idx[&q_vals[i]];
            roots[i + 1] = update(&mut nodes, roots[i], 0, m - 1, pos, q_vals[i]);
        }

        // ---------- 处理查询 ----------
        let mut ans = Vec::with_capacity(queries.len());

        for query in &queries {
            let l = query[0] as usize;
            let r = query[1] as usize;
            let len = (r - l + 1) as i64;

            // 检查区间内所有余数是否相等
            let (min_r, max_r) = query_segment_tree(1, 0, n - 1, l, r, &min_rem, &max_rem);
            if min_r != max_r {
                ans.push(-1);
                continue;
            }

            // 找到区间内 q 值的中位数
            let median = kth(&nodes, &sorted_q, roots[l], roots[r + 1], 0, m - 1, (len + 1) / 2);
            let mid_idx = value_to_idx[&median];

            // 计算小于中位数的元素个数和总和
            let (left_count, left_sum) = query_persistent(
                &nodes,
                roots[l],
                roots[r + 1],
                0,
                m - 1,
                0,
                mid_idx,
            );

            // 总元素个数和总和
            let total_count = len;
            let total_sum = nodes[roots[r + 1]].sum - nodes[roots[l]].sum;

            // 计算将所有数变为中位数的代价
            // 左侧代价：median * left_count - left_sum
            // 右侧代价：(total_sum - left_sum) - median * (total_count - left_count)
            let cost = median * left_count - left_sum + (total_sum - left_sum) - median * (total_count - left_count);
            ans.push(cost);
        }

        ans
    }
}

/// 构建普通线段树，维护区间内余数的最大值和最小值
fn build_segment_tree(
    node: usize,
    l: usize,
    r: usize,
    arr: &[i32],
    min_rem: &mut [i32],
    max_rem: &mut [i32],
) {
    if l == r {
        min_rem[node] = arr[l];
        max_rem[node] = arr[l];
        return;
    }
    let mid = (l + r) >> 1;
    let left = node << 1;
    let right = left | 1;
    build_segment_tree(left, l, mid, arr, min_rem, max_rem);
    build_segment_tree(right, mid + 1, r, arr, min_rem, max_rem);
    min_rem[node] = min_rem[left].min(min_rem[right]);
    max_rem[node] = max_rem[left].max(max_rem[right]);
}

/// 查询区间内余数的最大值和最小值
fn query_segment_tree(
    node: usize,
    l: usize,
    r: usize,
    ql: usize,
    qr: usize,
    min_rem: &[i32],
    max_rem: &[i32],
) -> (i32, i32) {
    if qr < l || r < ql {
        return (i32::MAX, i32::MIN);
    }
    if ql <= l && r <= qr {
        return (min_rem[node], max_rem[node]);
    }
    let mid = (l + r) >> 1;
    let left = node << 1;
    let right = left | 1;
    let (min1, max1) = query_segment_tree(left, l, mid, ql, qr, min_rem, max_rem);
    let (min2, max2) = query_segment_tree(right, mid + 1, r, ql, qr, min_rem, max_rem);
    (min1.min(min2), max1.max(max2))
}

/// 持久化线段树：单点更新，返回新根节点索引
fn update(nodes: &mut Vec<Node>, prev_root: usize, tl: usize, tr: usize, pos: usize, val: i64) -> usize {
    let new_root = nodes.len();
    nodes.push(nodes[prev_root].clone());

    if tl == tr {
        nodes[new_root].count += 1;
        nodes[new_root].sum += val;
        return new_root;
    }

    let tm = (tl + tr) >> 1;
    if pos <= tm {
        let new_left = update(nodes, nodes[prev_root].left, tl, tm, pos, val);
        nodes[new_root].left = new_left;
    } else {
        let new_right = update(nodes, nodes[prev_root].right, tm + 1, tr, pos, val);
        nodes[new_root].right = new_right;
    }

    let left = nodes[new_root].left;
    let right = nodes[new_root].right;
    nodes[new_root].count = nodes[left].count + nodes[right].count;
    nodes[new_root].sum = nodes[left].sum + nodes[right].sum;

    new_root
}

/// 持久化线段树：区间查询，返回 (count, sum)
fn query_persistent(
    nodes: &[Node],
    left_root: usize,
    right_root: usize,
    tl: usize,
    tr: usize,
    ql: usize,
    qr: usize,
) -> (i64, i64) {
    if qr < tl || tr < ql {
        return (0, 0);
    }
    if ql <= tl && tr <= qr {
        return (
            (nodes[right_root].count - nodes[left_root].count) as i64,
            nodes[right_root].sum - nodes[left_root].sum,
        );
    }

    let tm = (tl + tr) >> 1;
    let (c1, s1) = query_persistent(
        nodes,
        nodes[left_root].left,
        nodes[right_root].left,
        tl,
        tm,
        ql,
        qr,
    );
    let (c2, s2) = query_persistent(
        nodes,
        nodes[left_root].right,
        nodes[right_root].right,
        tm + 1,
        tr,
        ql,
        qr,
    );
    (c1 + c2, s1 + s2)
}

/// 持久化线段树：查找第 k 小的值
fn kth(
    nodes: &[Node],
    sorted_vals: &[i64],
    left_root: usize,
    right_root: usize,
    tl: usize,
    tr: usize,
    k: i64,
) -> i64 {
    if tl == tr {
        return sorted_vals[tl];
    }

    let tm = (tl + tr) >> 1;
    let left_count = (nodes[nodes[right_root].left].count - nodes[nodes[left_root].left].count) as i64;

    if k <= left_count {
        kth(
            nodes,
            sorted_vals,
            nodes[left_root].left,
            nodes[right_root].left,
            tl,
            tm,
            k,
        )
    } else {
        kth(
            nodes,
            sorted_vals,
            nodes[left_root].right,
            nodes[right_root].right,
            tm + 1,
            tr,
            k - left_count,
        )
    }
}
```
