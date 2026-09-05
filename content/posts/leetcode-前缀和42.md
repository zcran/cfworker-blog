---
title: "leetcode-前缀和42"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 检查是否区域内所有整数都被覆盖

给你一个二维整数数组 ranges 和两个整数 left 和 right 。每个 ranges[i] = [starti, endi] 表示一个从 starti 到 endi 的 闭区间 。

如果闭区间 [left, right] 内每个整数都被 ranges 中 至少一个 区间覆盖，那么请你返回 true ，否则返回 false 。

已知区间 ranges[i] = [starti, endi] ，如果整数 x 满足 starti <= x <= endi ，那么我们称整数x 被覆盖了。


```
impl Solution {
    /// 判断 [left, right] 区间内的每个整数是否都被至少一个区间覆盖
    ///
    /// 使用差分数组统计每个点被覆盖的次数
    /// 因为题目限制区间范围在 1..=50，差分数组大小固定为 52
    pub fn is_covered(ranges: Vec<Vec<i32>>, left: i32, right: i32) -> bool {
        const MAX: usize = 52;
        let mut diff = vec![0; MAX];

        // 差分标记：区间 [l, r] 覆盖，在 l 处 +1，在 r+1 处 -1
        for range in ranges {
            let l = range[0] as usize;
            let r = range[1] as usize + 1;
            diff[l] += 1;
            diff[r] -= 1;
        }

        // 前缀和计算每个点的实际覆盖次数
        let mut covered = 0;
        for i in 0..MAX {
            covered += diff[i];
            // 如果当前点在 [left, right] 范围内且未被覆盖，返回 false
            if i >= left as usize && i <= right as usize && covered == 0 {
                return false;
            }
        }

        true
    }
}
```
