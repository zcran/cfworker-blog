---
title: "leetcode-前缀和147"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 按规则计算统计结果

为了深入了解这些生物群体的生态特征，你们进行了大量的实地观察和数据采集。数组 arrayA 记录了各个生物群体数量数据，其中 arrayA[i] 表示第 i 个生物群体的数量。请返回一个数组 arrayB，该数组为基于数组 arrayA 中的数据计算得出的结果，其中 arrayB[i] 表示将第 i 个生物群体的数量从总体中排除后的其他数量的乘积。


```
impl Solution {
    pub fn statistical_result(a: Vec<i32>) -> Vec<i32> {
        let n = a.len();
        if n == 0 {
            return Vec::new();
        }

        let mut ans = vec![1; n];

        // 从左到右：ans[i] = a[0] * a[1] * ... * a[i-1]
        for i in 1..n {
            ans[i] = ans[i - 1] * a[i - 1];
        }

        // 从右到左：ans[i] *= a[i+1] * a[i+2] * ... * a[n-1]
        let mut suffix_product = 1;
        for i in (0..n - 1).rev() {
            suffix_product *= a[i + 1];
            ans[i] *= suffix_product;
        }

        ans
    }
}
```
