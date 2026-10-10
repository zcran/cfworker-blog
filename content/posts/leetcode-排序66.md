---
title: "leetcode-排序66"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 三角形类型

给你一个下标从 0 开始长度为 3 的整数数组 nums ，需要用它们来构造三角形。

如果一个三角形的所有边长度相等，那么这个三角形称为 equilateral 。
如果一个三角形恰好有两条边长度相等，那么这个三角形称为 isosceles 。
如果一个三角形三条边的长度互不相同，那么这个三角形称为 scalene 。

如果这个数组无法构成一个三角形，请你返回字符串 "none" ，否则返回一个字符串表示这个三角形的类型。


```
impl Solution {
    pub fn triangle_type(mut nums: Vec<i32>) -> String {
        // 排序后只需检查 nums[0] + nums[1] > nums[2] 即可判断能否构成三角形
        nums.sort_unstable();
        let [a, b, c] = [nums[0], nums[1], nums[2]];

        if a + b <= c {
            "none".to_string()
        } else if a == c {
            "equilateral".to_string()
        } else if a == b || b == c {
            "isosceles".to_string()
        } else {
            "scalene".to_string()
        }
    }
}
```
