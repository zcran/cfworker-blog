---
title: "leetcode-双指针70"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 数的平方等于两数乘积的方法数

给你两个整数数组 nums1 和 nums2 ，请你返回根据以下规则形成的三元组的数目（类型 1 和类型 2 ）：

类型 1：三元组 (i, j, k) ，如果 nums1[i]^2 == nums2[j] * nums2[k] 其中 0 <= i < nums1.length 且 0 <= j < k < nums2.length

类型 2：三元组 (i, j, k) ，如果 nums2[i]^2 == nums1[j] * nums1[k] 其中 0 <= i < nums2.length 且 0 <= j < k < nums1.length


```
impl Solution {
    pub fn num_triplets(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        count_triplets(&nums1, &nums2) + count_triplets(&nums2, &nums1)
    }
}

fn count_triplets(nums_a: &[i32], nums_b: &[i32]) -> i32 {
    use std::collections::HashMap;

    // 统计 nums_a 中每个数的平方出现次数
    let mut square_count = HashMap::with_capacity(nums_a.len());
    for &x in nums_a {
        *square_count.entry((x as i64) * (x as i64)).or_insert(0) += 1;
    }

    let mut total = 0;
    // 枚举 nums_b 中所有 j < k 的组合
    for j in 0..nums_b.len() {
        for k in j + 1..nums_b.len() {
            let product = (nums_b[j] as i64) * (nums_b[k] as i64);
            if let Some(&count) = square_count.get(&product) {
                total += count;
            }
        }
    }

    total
}
```
