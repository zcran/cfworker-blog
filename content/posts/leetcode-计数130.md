---
title: "leetcode-计数130"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 无人机方阵

在 「力扣挑战赛」 开幕式的压轴节目 「无人机方阵」中，每一架无人机展示一种灯光颜色。 无人机方阵通过两种操作进行颜色图案变换：

调整无人机的位置布局

切换无人机展示的灯光颜色

给定两个大小均为 N*M 的二维数组 source 和 target 表示无人机方阵表演的两种颜色图案，由于无人机切换灯光颜色的耗能很大，请返回从 source 到 target 最少需要多少架无人机切换灯光颜色。

注意： 调整无人机的位置布局时无人机的位置可以随意变动。


```
impl Solution {
    /// 无人机方阵：位置可任意调整，求最少需要切换颜色的无人机数量。
    ///
    /// 思路：颜色相同的无人机可以直接通过调整位置来匹配，无需切换颜色。
    /// 统计 source 和 target 中各颜色的数量差，无法匹配的部分即为需要切换颜色的数量。
    pub fn minimum_switching_times(source: Vec<Vec<i32>>, target: Vec<Vec<i32>>) -> i32 {
        // 颜色范围 1..=10^4，用 Vec 替代 HashMap，省去哈希开销且缓存友好
        let mut diff = vec![0i32; 10001];

        // 统计 source 中各颜色 +1
        for row in &source {
            for &c in row {
                diff[c as usize] += 1;
            }
        }

        // 统计 target 中各颜色 -1
        for row in &target {
            for &c in row {
                diff[c as usize] -= 1;
            }
        }

        // 正数表示 source 中多余的该颜色，负数表示 target 中缺少的该颜色。
        // 由于总数量相等，正数之和 = 负数绝对值之和 = 需要切换的颜色数。
        diff.into_iter().filter(|&x| x > 0).sum()
    }
}
```
