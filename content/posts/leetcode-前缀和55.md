---
title: "leetcode-前缀和55"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 相同元素的间隔之和

给你一个下标从 0 开始、由 n 个整数组成的数组 arr 。

arr 中两个元素的 间隔 定义为它们下标之间的 绝对差 。更正式地，arr[i] 和 arr[j] 之间的间隔是 |i - j| 。

返回一个长度为 n 的数组 intervals ，其中 intervals[i] 是 arr[i] 和 arr 中每个相同元素（与 arr[i] 的值相同）的 间隔之和 。

注意：|x| 是 x 的绝对值。


```
use std::collections::HashMap;

impl Solution {
    pub fn get_distances(arr: Vec<i32>) -> Vec<i64> {
        // 按值分组，记录每个值出现的所有下标
        let mut groups: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, &val) in arr.iter().enumerate() {
            groups.entry(val).or_insert_with(Vec::new).push(i);
        }

        let n = arr.len();
        let mut ans = vec![0; n];

        // 对每组相同值的下标计算间隔之和
        for indices in groups.values() {
            let m = indices.len();
            if m == 1 {
                continue; // 只有一个元素，间隔之和为0
            }

            // 计算第一个元素的间隔之和：所有其他下标到第一个下标的距离之和
            let first = indices[0];
            let mut total_dist: i64 = indices.iter().skip(1).map(|&x| (x - first) as i64).sum();
            ans[first] = total_dist;

            // 利用递推公式计算后续元素的间隔之和
            // 当从 indices[i-1] 移动到 indices[i] 时，距离变化为：
            // 对于左边 i 个元素，距离增加 d；对于右边 m-i 个元素，距离减少 d
            // 其中 d = indices[i] - indices[i-1]
            for i in 1..m {
                let d = (indices[i] - indices[i - 1]) as i64;
                // 左边有 i 个元素（包括当前），右边有 m-i 个元素
                total_dist += d * i as i64 - d * (m - i) as i64;
                ans[indices[i]] = total_dist;
            }
        }

        ans
    }
}
```
