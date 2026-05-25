---
title: "leetcode-树状树组34"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 物块放置查询

有一条无限长的数轴，原点在 0 处，沿着 x 轴 正 方向无限延伸。

给你一个二维数组 queries ，它包含两种操作：

1.操作类型 1 ：queries[i] = [1, x] 。在距离原点 x 处建一个障碍物。数据保证当操作执行的时候，位置 x 处 没有 任何障碍物。

2.操作类型 2 ：queries[i] = [2, x, sz] 。判断在数轴范围 [0, x] 内是否可以放置一个长度为 sz 的物块，这个物块需要 完全 放置在范围 [0, x] 内。如果物块与任何障碍物有重合，那么这个物块 不能 被放置，但物块可以与障碍物刚好接触。注意，你只是进行查询，并 不是 真的放置这个物块。每个查询都是相互独立的。

请你返回一个 boolean 数组results ，如果第 i 个操作类型 2 的操作你可以放置物块，那么 results[i] 为 true ，否则为 false 。

```
// ==================== 线段树通用接口 ====================
/// 线段树节点值的规范：定义如何合并两个节点
pub trait SegmentSpec {
    type Value: Clone;
    /// 单位元（初始值）
    fn identity() -> Self::Value;
    /// 合并两个节点的值
    fn combine(a: &Self::Value, b: &Self::Value) -> Self::Value;
}

/// 线段树（单点更新，区间查询）
pub struct SegmentTree<S: SegmentSpec> {
    data: Vec<S::Value>, // 线段树数组（1‑indexed）
    len: usize,          // 原始数组长度
}

impl<S: SegmentSpec> SegmentTree<S> {
    /// 根据初始值数组创建线段树
    pub fn new(init: &[S::Value]) -> Self {
        let len = init.len();
        let mut tree = Self {
            data: vec![S::identity(); len * 4],
            len,
        };
        tree.build(init, 0, len - 1, 1);
        tree
    }

    /// 递归构建线段树
    fn build(&mut self, init: &[S::Value], left: usize, right: usize, node: usize) {
        if left == right {
            self.data[node] = init[left].clone();
            return;
        }
        let mid = (left + right) / 2;
        self.build(init, left, mid, node * 2);
        self.build(init, mid + 1, right, node * 2 + 1);
        self.data[node] = S::combine(&self.data[node * 2], &self.data[node * 2 + 1]);
    }

    /// 单点更新（将位置 `pos` 的值设为 `value`）
    pub fn set(&mut self, pos: usize, value: S::Value) {
        assert!(pos < self.len);
        self._set(pos, value, 0, self.len - 1, 1);
    }

    fn _set(&mut self, pos: usize, value: S::Value, left: usize, right: usize, node: usize) {
        if left == right {
            self.data[node] = value;
            return;
        }
        let mid = (left + right) / 2;
        if pos <= mid {
            self._set(pos, value, left, mid, node * 2);
        } else {
            self._set(pos, value, mid + 1, right, node * 2 + 1);
        }
        self.data[node] = S::combine(&self.data[node * 2], &self.data[node * 2 + 1]);
    }

    /// 区间查询（闭区间 `[l, r]`）
    pub fn query(&mut self, l: usize, r: usize) -> S::Value {
        assert!(l <= r && r < self.len);
        self._query(l, r, 0, self.len - 1, 1)
    }

    fn _query(&mut self, l: usize, r: usize, left: usize, right: usize, node: usize) -> S::Value {
        if l <= left && right <= r {
            return self.data[node].clone();
        }
        let mid = (left + right) / 2;
        let mut res = S::identity();
        if l <= mid {
            res = S::combine(&res, &self._query(l, r, left, mid, node * 2));
        }
        if r > mid {
            res = S::combine(&res, &self._query(l, r, mid + 1, right, node * 2 + 1));
        }
        res
    }
}

// ==================== 本问题专用节点信息 ====================
/// 线段树节点存储的信息：
/// - `mid_split`: 区间内部是否存在分割点（即某个位置被标记）
/// - `left_split`: 区间最左边是否紧邻一个分割点（用于连接左右区间）
/// - `left`: 从区间左端点开始的最长连续空闲段长度（如果左边界不是紧邻分割点）
/// - `mid`: 区间内部（不包含两端）的最长连续空闲段长度
/// - `right`: 从区间右端点开始的最长连续空闲段长度（如果右边界不是紧邻分割点）
#[derive(Clone, Debug, Default)]
struct IntervalInfo {
    mid_split: bool,
    left_split: bool,
    left: i32,
    mid: i32,
    right: i32,
}

impl IntervalInfo {
    /// 普通空闲单元（未被分割）
    fn normal() -> Self {
        Self {
            mid_split: false,
            left_split: false,
            left: 1,
            mid: 0,
            right: 1,
        }
    }

    /// 分割点（已放置障碍物）
    fn split() -> Self {
        Self {
            mid_split: false,
            left_split: true,
            left: 0,
            mid: 0,
            right: 1,
        }
    }

    /// 获取当前区间内的最大连续空闲长度
    fn max_gap(&self) -> i32 {
        self.left.max(self.mid).max(self.right)
    }
}

/// 实现线段树合并规则
struct InfoSpec;

impl SegmentSpec for InfoSpec {
    type Value = IntervalInfo;

    fn identity() -> Self::Value {
        IntervalInfo::default()
    }

    /// 合并两个相邻区间 `a`（左）和 `b`（右）
    fn combine(a: &Self::Value, b: &Self::Value) -> Self::Value {
        let mid_split = b.left_split || a.mid_split || b.mid_split;
        let left_split = a.left_split;

        // 左半部分的 left 长度：
        // 如果 a 区间的中间部分没有被分割且 b 的左边界也没有分割，则可以与 b.left 连接
        let left = a.left
            + if !a.mid_split && !b.left_split {
                b.left
            } else {
                0
            };

        // 中间最大空闲段：
        // 可能是左半的 mid、右半的 mid，或者跨越中点的一段（需要左右边界不分割）
        let mid = if b.left_split {
            // b 的左边界被分割，此时不能跨越连接，只能取 a.right 和 b.left 的最大值
            a.right.max(b.left)
        } else {
            // 可以跨越连接
            a.right + b.left
        };
        let mid = a.mid.max(b.mid).max(mid);

        // 右半部分的 right 长度：
        let right = b.right
            + if !b.mid_split && !b.left_split {
                a.right
            } else {
                0
            };

        IntervalInfo {
            mid_split,
            left_split,
            left,
            mid,
            right,
        }
    }
}

// ==================== 主解法 ====================
impl Solution {
    /// 处理一系列仓库查询和障碍放置
    ///
    /// 查询格式：
    /// - [1, x]：在位置 x 放置一个障碍物（分割点）
    /// - [2, x, s]：询问区间 [0, x-1] 内是否存在长度 ≥ s 的连续空闲段
    ///
    /// 返回每个类型 2 查询的布尔结果
    pub fn get_results(queries: Vec<Vec<i32>>) -> Vec<bool> {
        // 找出所有查询中涉及的最大坐标（位置 x），用于确定线段树大小
        let max_pos = queries.iter().map(|q| q[1]).max().unwrap() as usize;
        let n = max_pos + 1; // 坐标范围 [0, max_pos]

        // 初始所有位置都是空闲的（IntervalInfo::normal）
        let init = vec![IntervalInfo::normal(); n];
        let mut seg_tree = SegmentTree::<InfoSpec>::new(&init);

        queries
            .into_iter()
            .filter_map(|q| match q[0] {
                1 => {
                    // 放置障碍物：将位置 x 更新为 split 节点
                    let x = q[1] as usize;
                    seg_tree.set(x, IntervalInfo::split());
                    None
                }
                2 => {
                    // 查询区间 [0, x-1] 的最大空闲长度（原算法要求）
                    let x = q[1] as usize;
                    let need_len = q[2];
                    // 当 x == 0 时区间为空，直接返回 false（原代码未处理，但此处防御）
                    if x == 0 {
                        Some(false)
                    } else {
                        let info = seg_tree.query(0, x - 1);
                        Some(info.max_gap() >= need_len)
                    }
                }
                _ => unreachable!(),
            })
            .collect()
    }
}
```
