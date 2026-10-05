---
title: "leetcode-双指针135"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 聚合两个时间序列

给你两个二维整数数组 series1 和 series2。

两个序列中的每个元素都表示为 [timestamp, value]，其中：

timestamp 是表示时间的整数。
value 是表示该时间点对应值的整数。

每个数组都按照 timestamp 的 严格递增 顺序排列。

若某个序列中某个时间戳 缺失 ，且该序列中存在更晚的时间戳，则将该缺失时间戳的值设为下一个更晚时间戳对应的值。否则，该时间点的值视为 0。

聚合序列 通过以下方式构造：对于两个序列中出现过的每个时间戳，将两个序列在该时间戳对应的值相加。

返回聚合后的序列，格式为二维整数数组 [timestamp, summedValue]，并按照 timestamp 严格递增 排序。

如果一个数组中的每个元素都严格大于前一个元素，则称该数组为 严格递增 。


```
impl Solution {
    pub fn aggregate_time_series(
        series1: Vec<Vec<i32>>,
        series2: Vec<Vec<i32>>,
    ) -> Vec<Vec<i32>> {
        let n = series1.len();
        let m = series2.len();
        let mut ans = Vec::with_capacity(n + m);
        let (mut i, mut j) = (0usize, 0usize);

        // 双指针归并：每次取较小时间戳，值相加（另一序列若已无更早时间戳，
        // 根据定义其值应沿用下一个更晚时间戳的值，因此当前指针所指即为该值）
        while i < n && j < m {
            let t1 = series1[i][0];
            let t2 = series2[j][0];
            let sum = series1[i][1] + series2[j][1];

            if t1 < t2 {
                ans.push(vec![t1, sum]);
                i += 1;
            } else if t1 > t2 {
                ans.push(vec![t2, sum]);
                j += 1;
            } else {
                ans.push(vec![t1, sum]);
                i += 1;
                j += 1;
            }
        }

        // 追加剩余元素（其值已由自身给出）
        ans.extend_from_slice(&series1[i..]);
        ans.extend_from_slice(&series2[j..]);
        ans
    }
}
```
