---
title: "leetcode-线段树9"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 操作后最大活跃区段数 II

给你一个长度为 n 的二进制字符串 s ，其中：

· '1' 表示一个 活跃 区段。
· '0' 表示一个 非活跃 区段。

你最多可以进行一次 操作 来最大化 s 中活跃区段的数量。在一次操作中，你可以：

· 将一个被 '0' 包围的连续 '1' 区块转换为全 '0'。
· 然后，将一个被 '1' 包围的连续 '0' 区块转换为全 '1'。

此外，你还有一个 二维数组 queries，其中 queries[i] = [li, ri] 表示子字符串 s[li...ri]。

对于每个查询，确定在对子字符串 s[li...ri] 进行最优交换后，字符串 s 中 可能的最大 活跃区段数。

返回一个数组 answer，其中 answer[i] 是 queries[i] 的结果。

注意

· 对于每个查询，仅对 s[li...ri] 处理时，将其看作是在两端都加上一个 '1' 后的字符串，形成 t = '1' + s[li...ri] + '1'。这些额外的 '1' 不会对最终的活跃区段数有贡献。
· 各个查询相互独立。


```
use std::collections::HashMap;

struct SegTree {
    n: usize,
    s: Vec<u8>,
    f: Vec<i32>,
    left: [Vec<usize>; 2],
    right: [Vec<usize>; 2],
    pre: Vec<i32>,
}

impl SegTree {
    fn new(s: &str) -> Self {
        let s_bytes = s.as_bytes();
        let n = s_bytes.len();

        let mut left = [vec![0; n], vec![0; n]];
        let mut right = [vec![n; n], vec![n; n]];

        let mut pre = vec![0; n + 1];
        let mut one_cnt = 0;
        let mut zero_cnt = 0;
        for i in 0..n {
            if s_bytes[i] == b'1' {
                one_cnt += 1;
                zero_cnt = 0;
                left[1][i] = i - one_cnt + 1;
                pre[i + 1] = pre[i] + 1;
            } else {
                one_cnt = 0;
                zero_cnt += 1;
                left[0][i] = i - zero_cnt + 1;
                pre[i + 1] = pre[i];
            }
        }

        let mut one_cnt = 0;
        let mut zero_cnt = 0;
        for i in (0..n).rev() {
            if s_bytes[i] == b'1' {
                one_cnt += 1;
                zero_cnt = 0;
                right[1][i] = i + one_cnt - 1;
            } else {
                one_cnt = 0;
                zero_cnt += 1;
                right[0][i] = i + zero_cnt - 1;
            }
        }

        let mut tree = SegTree {
            n,
            s: s_bytes.to_vec(),
            f: vec![0; 4 * n],
            left,
            right,
            pre,
        };
        tree.build(1, 0, n - 1);
        tree
    }

    fn build(&mut self, node: usize, l: usize, r: usize) {
        if l == r {
            self.f[node] = 0;
            return;
        }
        let mid = (l + r) >> 1;
        self.build(node << 1, l, mid);
        self.build(node << 1 | 1, mid + 1, r);
        self.f[node] = self.pushup(self.f[node << 1], self.f[node << 1 | 1], l, mid, r);
    }

    fn pushup(&self, left_val: i32, right_val: i32, l: usize, mid: usize, r: usize) -> i32 {
        let mut res = left_val.max(right_val);

        // 在跨左右边界的区间中，寻找 "0...01...10...0" 模式
        // 即找到两个零区间和一个一区间

        // 情况1：mid 是 '1'，以这个 '1' 为中心的一区间
        if self.s[mid] == b'1' {
            let l1 = self.left[1][mid];
            let r1 = self.right[1][mid];

            // 需要左边有零区间，右边也有零区间
            if l1 > l && r1 < r {
                let l0 = l.max(self.left[0][l1 - 1]);
                let r0 = r.min(self.right[0][r1 + 1]);
                let ones = self.pre[r0 + 1] - self.pre[l0];
                let len = (r0 - l0 + 1) as i32;
                res = res.max(len - ones);
            }
        }
        // 情况2：mid 是 '0'，这个 '0' 属于某个零区间
        else {
            // 情况2a：在左侧寻找 "010" 模式
            // 即找到一个一区间，它的左边有零区间，右边也有零区间
            // 具体来说：在 mid 左侧找一个 '1'，这个 '1' 左侧有 '0'，且这个 '1' 到 mid 之间都是 '0'
            let r0_mid = self.right[0][mid];
            // 如果 mid 所在零区间的右边界没有超出 r，说明这个零区间在查询范围内
            if r0_mid < r {
                // 在零区间右侧找一区间
                let r1 = self.right[1][r0_mid + 1];
                if r1 <= r {
                    // 在一区间右侧找另一个零区间
                    if r1 < r {
                        let r0 = r.min(self.right[0][r1 + 1]);
                        let l0 = l.max(self.left[0][mid]);
                        // 确保 l0 和 r0 之间有完整的 "010" 模式
                        if l0 <= mid && r0 > r1 {
                            let ones = self.pre[r0 + 1] - self.pre[l0];
                            let len = (r0 - l0 + 1) as i32;
                            res = res.max(len - ones);
                        }
                    }
                }
            }

            // 情况2b：在右侧寻找 "010" 模式
            let l0_mid = self.left[0][mid];
            if l0_mid > l {
                let l1 = self.left[1][l0_mid - 1];
                if l1 >= l {
                    if l1 > l {
                        let l0 = l.max(self.left[0][l1 - 1]);
                        let r0 = r.min(self.right[0][mid]);
                        if l0 <= l1 && r0 >= mid {
                            let ones = self.pre[r0 + 1] - self.pre[l0];
                            let len = (r0 - l0 + 1) as i32;
                            res = res.max(len - ones);
                        }
                    }
                }
            }
        }

        res
    }

    fn query(&self, start: usize, end: usize) -> i32 {
        if start > end {
            return 0;
        }
        self._query(1, 0, self.n - 1, start, end)
    }

    fn _query(&self, node: usize, l: usize, r: usize, start: usize, end: usize) -> i32 {
        if start == l && end == r {
            return self.f[node];
        }

        let mid = (l + r) >> 1;
        if end <= mid {
            return self._query(node << 1, l, mid, start, end);
        }
        if start > mid {
            return self._query(node << 1 | 1, mid + 1, r, start, end);
        }

        let left_part = self._query(node << 1, l, mid, start, mid);
        let right_part = self._query(node << 1 | 1, mid + 1, r, mid + 1, end);
        self.pushup(left_part, right_part, start, mid, end)
    }
}

impl Solution {
    pub fn max_active_sections_after_trade(s: String, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let tree = SegTree::new(&s);
        let total_ones = tree.pre[tree.n];

        let mut ans = Vec::with_capacity(queries.len());
        for query in &queries {
            let l = query[0] as usize;
            let r = query[1] as usize;
            let gain = tree.query(l, r);
            ans.push(total_ones + gain);
        }

        ans
    }
}
```
