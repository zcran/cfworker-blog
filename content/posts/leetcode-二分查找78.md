---
title: "leetcode-二分查找78"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 不重叠区间的最大得分

给你一个二维整数数组 intervals，其中 intervals[i] = [li, ri, weighti]。区间 i 的起点为 li，终点为 ri，权重为 weighti。你最多可以选择 4 个互不重叠 的区间。所选择区间的 得分 定义为这些区间权重的总和。

返回一个至多包含 4 个下标且 字典序最小 的数组，表示从 intervals 中选中的互不重叠且得分最大的区间。

如果两个区间没有任何重叠点，则称二者 互不重叠 。特别地，如果两个区间共享左边界或右边界，也认为二者重叠。


```
impl Solution {
    pub fn maximum_weight(intervals: Vec<Vec<i32>>) -> Vec<i32> {
        let n = intervals.len();
        // (右端点, 左端点, 权重, 原始下标)
        let mut arr: Vec<(i32, i32, i64, usize)> = intervals
            .iter()
            .enumerate()
            .map(|(i, v)| (v[1], v[0], v[2] as i64, i))
            .collect();
        arr.sort_unstable_by_key(|x| x.0);

        // dp[i][j] 表示前 i 个区间中选 j 个的最大权重
        // best[i][j] 表示对应的字典序最小的下标集合
        let mut dp = vec![[0_i64; 5]; n + 1];
        let mut best: Vec<[Vec<i32>; 5]> = vec![Default::default(); n + 1];

        for i in 0..n {
            let (r, l, w, idx) = arr[i];
            let _ = r;
            // 二分找到右端点严格小于 l 的区间数量
            let k = arr[..i].partition_point(|x| x.0 < l);

            for j in 1..5 {
                let skip = dp[i][j];
                let take = dp[k][j - 1] + w;

                if take > skip {
                    let mut cand = best[k][j - 1].clone();
                    cand.push(idx as i32);
                    cand.sort_unstable();
                    dp[i + 1][j] = take;
                    best[i + 1][j] = cand;
                } else if take < skip {
                    dp[i + 1][j] = skip;
                    best[i + 1][j] = best[i][j].clone();
                } else {
                    // 权重相同，取字典序更小的
                    let mut cand = best[k][j - 1].clone();
                    cand.push(idx as i32);
                    cand.sort_unstable();
                    dp[i + 1][j] = skip;
                    best[i + 1][j] = if cand < best[i][j] {
                        cand
                    } else {
                        best[i][j].clone()
                    };
                }
            }
        }

        best[n][4].clone()
    }
}
```
