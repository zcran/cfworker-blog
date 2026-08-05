---
title: "leetcode-枚举8"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计好三元组

给你一个整数数组 arr ，以及 a、b 、c 三个整数。请你统计其中好三元组的数量。

如果三元组 (arr[i], arr[j], arr[k]) 满足下列全部条件，则认为它是一个 好三元组 。

0 <= i < j < k < arr.length
|arr[i] - arr[j]| <= a
|arr[j] - arr[k]| <= b
|arr[i] - arr[k]| <= c

其中 |x| 表示 x 的绝对值。

返回 好三元组的数量 。


```
impl Solution {
    /// 统计满足条件的三元组数量：
    /// i < j < k 且 |arr[i]-arr[j]|<=a, |arr[j]-arr[k]|<=b, |arr[i]-arr[k]|<=c
    pub fn count_good_triplets(arr: Vec<i32>, a: i32, b: i32, c: i32) -> i32 {
        let n = arr.len();
        let mut count = 0;

        // 枚举中间位置 j，减少重复计算
        for j in 1..n - 1 {
            let aj = arr[j];

            // 收集所有满足 |arr[i] - aj| <= a 的左侧索引 i
            let mut left_indices = Vec::with_capacity(j);
            for i in 0..j {
                if (arr[i] - aj).abs() <= a {
                    left_indices.push(i);
                }
            }

            // 如果左侧没有满足条件的，跳过
            if left_indices.is_empty() {
                continue;
            }

            // 枚举右侧 k，同时检查与 j 和所有左侧 i 的条件
            for k in j + 1..n {
                let ak = arr[k];
                // 先检查 |aj - ak| <= b，不满足则跳过
                if (aj - ak).abs() > b {
                    continue;
                }

                // 统计左侧满足 |arr[i] - ak| <= c 的数量
                for &i in &left_indices {
                    if (arr[i] - ak).abs() <= c {
                        count += 1;
                    }
                }
            }
        }

        count
    }
}
```
