---
title: "leetcode-计数130"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 主要元素

数组中占比超过一半的元素称之为主要元素。给你一个 整数 数组，找出其中的主要元素。若没有，返回 -1 。请设计时间复杂度为 O(N) 、空间复杂度为 O(1) 的解决方案。


```
impl Solution {
    /// 找出数组中的主要元素（出现次数严格超过 n/2）。
    ///
    /// 算法：Boyer-Moore 摩尔投票法。
    /// 第一趟扫描用 O(1) 空间确定候选元素；
    /// 第二趟扫描验证该候选是否确实超过半数。
    pub fn majority_element(nums: Vec<i32>) -> i32 {
        // 第一趟：投票找候选
        let (mut candidate, mut count) = (0, 0);
        for &num in &nums {
            if count == 0 {
                candidate = num;
            }
            count += if num == candidate { 1 } else { -1 };
        }

        // 第二趟：验证候选
        let occurs = nums.iter().filter(|&&x| x == candidate).count();
        if occurs > nums.len() / 2 {
            candidate
        } else {
            -1
        }
    }
}
```
