---
title: "leetcode-排序91"
date: 2026-10-05T00:35:06+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 合并排序的数组

给定两个排序后的数组 A 和 B，其中 A 的末端有足够的缓冲空间容纳 B。 编写一个方法，将 B 合并入 A 并排序。

初始化 A 和 B 的元素数量分别为 m 和 n。


```
impl Solution {
    pub fn merge(a: &mut Vec<i32>, m: i32, b: &mut Vec<i32>, n: i32) {
        // 从后往前归并，避免覆盖 A 中尚未处理的元素
        let mut i = m as usize; // A 中剩余元素个数
        let mut j = n as usize; // B 中剩余元素个数
        let mut k = i + j;      // 写入位置（从末尾开始）

        while j > 0 {
            if i > 0 && a[i - 1] > b[j - 1] {
                a[k - 1] = a[i - 1];
                i -= 1;
            } else {
                a[k - 1] = b[j - 1];
                j -= 1;
            }
            k -= 1;
        }
        // 若 B 已耗尽，A 剩余部分本就在正确位置，无需处理
    }
}
```
