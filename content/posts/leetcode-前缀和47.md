---
title: "leetcode-前缀和47"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 描述绘画结果

给你一个细长的画，用数轴表示。这幅画由若干有重叠的线段表示，每个线段有 独一无二 的颜色。给你二维整数数组 segments ，其中 segments[i] = [starti, endi, colori] 表示线段为 半开区间 [starti, endi) 且颜色为 colori 。

线段间重叠部分的颜色会被 混合 。如果有两种或者更多颜色混合时，它们会形成一种新的颜色，用一个 集合 表示这个混合颜色。

比方说，如果颜色 2 ，4 和 6 被混合，那么结果颜色为 {2,4,6} 。

为了简化题目，你不需要输出整个集合，只需要用集合中所有元素的 和 来表示颜色集合。

你想要用 最少数目 不重叠 半开区间 来 表示 这幅混合颜色的画。这些线段可以用二维数组 painting 表示，其中 painting[j] = [leftj, rightj, mixj] 表示一个 半开区间[leftj, rightj) 的颜色 和 为 mixj 。

比方说，这幅画由 segments = [[1,4,5],[1,7,7]] 组成，那么它可以表示为 painting = [[1,4,12],[4,7,7]] ，因为：
    [1,4) 由颜色 {5,7} 组成（和为 12），分别来自第一个线段和第二个线段。
    [4,7) 由颜色 {7} 组成，来自第二个线段。

请你返回二维数组 painting ，它表示最终绘画的结果（没有 被涂色的部分不出现在结果中）。你可以按 任意顺序 返回最终数组的结果。

半开区间 [a, b) 是数轴上点 a 和点 b 之间的部分，包含 点 a 且 不包含 点 b 。


```
impl Solution {
    /// 用最少数目的不重叠半开区间表示混合颜色的画
    ///
    /// 使用差分思想 + BTreeMap 只存储变化点，避免遍历整个坐标轴
    /// 当颜色和发生变化时，生成一个区间
    pub fn split_painting(segments: Vec<Vec<i32>>) -> Vec<Vec<i64>> {
        use std::collections::BTreeMap;

        // 差分数组：只在端点处记录颜色和的变化
        let mut diff = BTreeMap::new();
        for seg in segments {
            let l = seg[0];
            let r = seg[1];
            let color = seg[2] as i64;

            *diff.entry(l).or_insert(0) += color;
            *diff.entry(r).or_insert(0) -= color;
        }

        let mut res = Vec::new();
        let mut prev_pos = 0;
        let mut cur_sum = 0i64;

        for (&pos, &delta) in diff.iter() {
            // 当前区间 [prev_pos, pos) 有颜色
            if cur_sum != 0 {
                res.push(vec![prev_pos as i64, pos as i64, cur_sum]);
            }

            // 更新状态
            prev_pos = pos;
            cur_sum += delta;
        }

        res
    }
}
```
