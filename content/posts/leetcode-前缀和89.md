---
title: "leetcode-前缀和89"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 与车相交的点

给你一个下标从 0 开始的二维整数数组 nums 表示汽车停放在数轴上的坐标。对于任意下标 i，nums[i] = [starti, endi] ，其中 starti 是第 i 辆车的起点，endi 是第 i 辆车的终点。

返回数轴上被车 任意部分 覆盖的整数点的数目。


```
impl Solution {
    pub fn number_of_points(nums: Vec<Vec<i32>>) -> i32 {
        // 差分数组优化：记录区间覆盖变化
        // 找到最大坐标以确定数组大小
        let max_end = nums.iter().map(|v| v[1]).max().unwrap_or(0);
        let mut diff = vec![0; (max_end + 2) as usize];

        // 构建差分：起点+1，终点后-1
        for interval in &nums {
            diff[interval[0] as usize] += 1;
            diff[(interval[1] + 1) as usize] -= 1;
        }

        // 前缀和统计被覆盖的点
        let mut covered = 0;
        let mut current = 0;
        for i in 0..=max_end as usize {
            current += diff[i];
            if current > 0 {
                covered += 1;
            }
        }

        covered
    }
}
```
