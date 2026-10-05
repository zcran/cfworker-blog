---
title: "leetcode-贪心34"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 将数组分成和相等的三个部分

给你一个整数数组 arr，只有可以将其划分为三个和相等的 非空 部分时才返回 true，否则返回 false。

形式上，如果可以找出索引 i + 1 < j 且满足 (arr[0] + arr[1] + ... + arr[i] == arr[i + 1] + arr[i + 2] + ... + arr[j - 1] == arr[j] + arr[j + 1] + ... + arr[arr.length - 1]) 就可以将数组三等分。


```
impl Solution {
    pub fn can_three_parts_equal_sum(arr: Vec<i32>) -> bool {
        let total: i32 = arr.iter().sum();

        // 总和不能被 3 整除，直接无解
        if total % 3 != 0 {
            return false;
        }

        let target = total / 3;
        let mut cur = 0;
        let mut parts = 0;

        // 前缀和达到 target 即切出一段；找到两段后，剩余部分必须非空
        for i in 0..arr.len() - 1 {
            cur += arr[i];
            if cur == target {
                parts += 1;
                if parts == 2 {
                    return true;
                }
                cur = 0;
            }
        }

        false
    }
}
```
