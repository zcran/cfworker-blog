---
title: "leetcode-数组69"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 数组中的峰值 II

给你一个长度为 n 的整数数组 nums 和一个二维整数数组 queries。

如果满足以下条件，子数组 nums[i..j] 被称为 峰值子数组：

·其长度 至少 为 3。
·存在一个下标 k 使得 i < k < j 且：
    · nums[k] > nums[k - 1]
    · nums[k] > nums[k + 1]

你需要处理以下两种类型的查询：

· [1, li, ri]：计算完全包含在 nums[li..ri] 中的 峰值子数组 的数量。
· [2, indexi, vali]：将 nums[indexi] 更新为 vali。此更新适用于所有后续查询。

返回一个数组 answer，其中 answer[i] 是按出现顺序排列的第 i 个类型 1 查询的答案。

子数组 是数组中连续的 非空 元素序列。


```
// 线段树节点：维护区间内峰值子数组计数及前后缀信息
#[derive(Clone, Copy)]
struct Data {
    cnt: i64,        // 区间内峰值子数组的数量
    pre: i32,        // 从区间左端起不含峰的最长前缀长度
    suf: i32,        // 从区间右端起不含峰的最长后缀长度
    len: i32,        // 区间长度
    has_peak: bool,  // 区间内是否存在峰
}

impl Data {
    // 合并左右子区间信息
    fn merge(a: Data, b: Data) -> Data {
        let cnt = a.cnt + b.cnt + (a.len as i64) * (b.len as i64) - (a.suf as i64) * (b.pre as i64);
        let pre = if a.has_peak { a.pre } else { a.len + b.pre };
        let suf = if b.has_peak { b.suf } else { b.len + a.suf };
        Data {
            cnt,
            pre,
            suf,
            len: a.len + b.len,
            has_peak: a.has_peak || b.has_peak,
        }
    }
}

struct SegmentTree {
    n: usize,
    tree: Vec<Data>,
}

impl SegmentTree {
    fn new(a: &[i32]) -> Self {
        let n = a.len();
        // 4 倍容量足够覆盖线段树节点数
        let mut tree = vec![Data { cnt: 0, pre: 1, suf: 1, len: 1, has_peak: false }; 4 * n];
        let mut st = SegmentTree { n, tree };
        if n > 0 {
            st.build(a, 1, 0, n - 1);
        }
        st
    }

    fn build(&mut self, a: &[i32], node: usize, l: usize, r: usize) {
        if l == r {
            // 叶子：判断是否是峰（非首尾且严格大于左右邻居）
            let has_peak = l > 0 && l < self.n - 1 && a[l - 1] < a[l] && a[l] > a[l + 1];
            self.tree[node] = Data { cnt: 0, pre: 1, suf: 1, len: 1, has_peak };
            return;
        }
        let m = (l + r) / 2;
        self.build(a, node * 2, l, m);
        self.build(a, node * 2 + 1, m + 1, r);
        self.tree[node] = Data::merge(self.tree[node * 2], self.tree[node * 2 + 1]);
    }

    fn update(&mut self, node: usize, l: usize, r: usize, i: usize) {
        if l == r {
            // 翻转当前叶子的峰状态
            self.tree[node].has_peak = !self.tree[node].has_peak;
            return;
        }
        let m = (l + r) / 2;
        if i <= m {
            self.update(node * 2, l, m, i);
        } else {
            self.update(node * 2 + 1, m + 1, r, i);
        }
        self.tree[node] = Data::merge(self.tree[node * 2], self.tree[node * 2 + 1]);
    }

    fn query(&self, node: usize, l: usize, r: usize, ql: usize, qr: usize) -> Data {
        if ql <= l && r <= qr {
            return self.tree[node];
        }
        let m = (l + r) / 2;
        if qr <= m {
            return self.query(node * 2, l, m, ql, qr);
        }
        if ql > m {
            return self.query(node * 2 + 1, m + 1, r, ql, qr);
        }
        Data::merge(
            self.query(node * 2, l, m, ql, qr),
            self.query(node * 2 + 1, m + 1, r, ql, qr),
        )
    }

    // 对外接口：更新位置 i 的峰状态
    fn update_at(&mut self, i: usize) {
        self.update(1, 0, self.n - 1, i);
    }

    // 对外接口：查询区间 [ql, qr] 内峰值子数组数量
    fn query_range(&self, ql: usize, qr: usize) -> i64 {
        self.query(1, 0, self.n - 1, ql, qr).cnt
    }
}

impl Solution {
    pub fn count_of_peaks(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i64> {
        let n = nums.len();
        let mut nums = nums;
        let mut t = SegmentTree::new(&nums);
        let mut ans = Vec::new();

        for q in &queries {
            if q[0] == 1 {
                // 类型 1：查询 [q[1], q[2]] 内的峰值子数组数量
                ans.push(t.query_range(q[1] as usize, q[2] as usize));
                continue;
            }

            // 类型 2：将 nums[i] 更新为 v
            let i = q[1] as usize;
            let v = q[2];

            // 更新可能受影响的三个位置 i-1, i, i+1 的峰状态
            if i > 1 {
                let old_has = nums[i - 2] < nums[i - 1] && nums[i - 1] > nums[i];
                let new_has = nums[i - 2] < nums[i - 1] && nums[i - 1] > v;
                if new_has != old_has {
                    t.update_at(i - 1);
                }
            }

            if i > 0 && i < n - 1 {
                let old_has = nums[i - 1] < nums[i] && nums[i] > nums[i + 1];
                let new_has = nums[i - 1] < v && v > nums[i + 1];
                if new_has != old_has {
                    t.update_at(i);
                }
            }

            if i < n - 2 {
                let old_has = nums[i] < nums[i + 1] && nums[i + 1] > nums[i + 2];
                let new_has = v < nums[i + 1] && nums[i + 1] > nums[i + 2];
                if new_has != old_has {
                    t.update_at(i + 1);
                }
            }

            nums[i] = v;
        }

        ans
    }
}
```
