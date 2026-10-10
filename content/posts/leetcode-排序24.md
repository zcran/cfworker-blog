---
title: "leetcode-排序24"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最小绝对差

给你个整数数组 arr，其中每个元素都 不相同。

请你找到所有具有最小绝对差的元素对，并且按升序的顺序返回。

每对元素对 [a,b] 如下：

a , b 均为数组 arr 中的元素
a < b
b - a 等于 arr 中任意两个元素的最小绝对差


```
impl Solution {
    /// 找出所有具有最小绝对差的元素对，按升序返回
    ///
    /// 思路：排序后，最小绝对差必然出现在相邻元素之间。
    /// 一次线性扫描相邻差：更小则重置结果，相等则追加。
    /// 时间 O(n log n)，额外空间 O(1)（不含输出）。
    pub fn minimum_abs_difference(mut arr: Vec<i32>) -> Vec<Vec<i32>> {
        arr.sort_unstable();

        let mut min_diff = i32::MAX;
        let mut ans = Vec::new();

        for w in arr.windows(2) {
            let diff = w[1] - w[0];
            if diff < min_diff {
                // 发现更小的差，之前的结果全部作废
                min_diff = diff;
                ans.clear();
                ans.push(w.to_vec());
            } else if diff == min_diff {
                ans.push(w.to_vec());
            }
        }

        ans
    }
}
```
