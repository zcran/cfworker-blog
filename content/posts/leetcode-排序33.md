---
title: "leetcode-排序33"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 去掉最低工资和最高工资后的工资平均值

给你一个整数数组 salary ，数组里每个数都是 唯一 的，其中 salary[i] 是第 i 个员工的工资。

请你返回去掉最低工资和最高工资以后，剩下员工工资的平均值。


```
impl Solution {
    /// 去掉最低和最高工资后，剩余员工工资的平均值
    ///
    /// 思路：一次遍历同时求总和、最小值、最大值，
    /// 再减去两端后除以 (n - 2)。工资互不相同，无需担心重复。
    /// 时间 O(n)，额外空间 O(1)。
    pub fn average(salary: Vec<i32>) -> f64 {
        let mut sum = 0i64;
        let mut min = i32::MAX;
        let mut max = i32::MIN;

        for &s in &salary {
            sum += s as i64;
            min = min.min(s);
            max = max.max(s);
        }

        let remaining = sum - min as i64 - max as i64;
        remaining as f64 / (salary.len() - 2) as f64
    }
}
```
