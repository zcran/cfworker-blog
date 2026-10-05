---
title: "leetcode-双指针111"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 切蛋糕的最小总开销 I

有一个 m x n 大小的矩形蛋糕，需要切成 1 x 1 的小块。

给你整数 m ，n 和两个数组：

· horizontalCut 的大小为 m - 1 ，其中 horizontalCut[i] 表示沿着水平线 i 切蛋糕的开销。
· verticalCut 的大小为 n - 1 ，其中 verticalCut[j] 表示沿着垂直线 j 切蛋糕的开销。

一次操作中，你可以选择任意不是 1 x 1 大小的矩形蛋糕并执行以下操作之一：

1. 沿着水平线 i 切开蛋糕，开销为 horizontalCut[i] 。
2. 沿着垂直线 j 切开蛋糕，开销为 verticalCut[j] 。

每次操作后，这块蛋糕都被切成两个独立的小蛋糕。

每次操作的开销都为最开始对应切割线的开销，并且不会改变。

请你返回将蛋糕全部切成 1 x 1 的蛋糕块的 最小 总开销。


```
impl Solution {
    /// 将 m x n 蛋糕切成 1 x 1 小块的最小总开销。
    ///
    /// 思路：贪心（类似哈夫曼）。
    /// 每次选择开销最大的切割线先切，因为它会被重复使用更多次。
    /// 具体地，维护当前横向块数 h 和纵向块数 v（初始均为 1）：
    ///   - 若 horizontal_cut 中最大开销 >= vertical_cut 中最大开销，则切横向，
    ///     开销累加 maxH * v，纵向块数 v 加 1；
    ///   - 否则切纵向，开销累加 maxV * h，横向块数 h 加 1。
    /// 两条切割数组分别降序排序后用双指针扫描。
    ///
    /// 时间复杂度 O(m log m + n log n)，空间复杂度 O(1)。
    pub fn minimum_cost(m: i32, n: i32, horizontal_cut: Vec<i32>, vertical_cut: Vec<i32>) -> i32 {
        let mut h = horizontal_cut;
        let mut v = vertical_cut;
        h.sort_unstable_by(|a, b| b.cmp(a));
        v.sort_unstable_by(|a, b| b.cmp(a));

        let (mut i, mut j) = (0usize, 0usize);
        // 当前横向、纵向的块数
        let mut hp = 1i64;
        let mut vp = 1i64;
        let mut ans = 0i64;

        while i < h.len() || j < v.len() {
            // 取两边当前最大开销比较（越界时视为 -1）
            let hc = if i < h.len() { h[i] as i64 } else { -1 };
            let vc = if j < v.len() { v[j] as i64 } else { -1 };

            if hc >= vc {
                ans += hc * vp;
                hp += 1;
                i += 1;
            } else {
                ans += vc * hp;
                vp += 1;
                j += 1;
            }
        }

        ans as i32
    }
}
```
