---
title: "leetcode-双指针67"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 数组中的 k 个最强值

给你一个整数数组 arr 和一个整数 k 。

设 m 为数组的中位数，只要满足下述两个前提之一，就可以判定 arr[i] 的值比 arr[j] 的值更强：

 |arr[i] - m| > |arr[j] - m|
 |arr[i] - m| == |arr[j] - m|，且 arr[i] > arr[j]

请返回由数组中最强的 k 个值组成的列表。答案可以以 任意顺序 返回。

中位数 是一个有序整数列表中处于中间位置的值。形式上，如果列表的长度为 n ，那么中位数就是该有序列表（下标从 0 开始）中位于 ((n - 1) / 2) 的元素。

例如 arr = [6, -3, 7, 2, 11]，n = 5：数组排序后得到 arr = [-3, 2, 6, 7, 11] ，数组的中间位置为 m = ((5 - 1) / 2) = 2 ，中位数 arr[m] 的值为 6 。

例如 arr = [-7, 22, 17, 3]，n = 4：数组排序后得到 arr = [-7, 3, 17, 22] ，数组的中间位置为 m = ((4 - 1) / 2) = 1 ，中位数 arr[m] 的值为 3 。



```
impl Solution {
    pub fn get_strongest(mut arr: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let n = arr.len();
        let mid = (n - 1) / 2;

        // 找到中位数（部分排序即可）
        arr.select_nth_unstable(mid);
        let median = arr[mid];

        // 使用自定义排序找到最强的 k 个元素
        // 比较规则：先按距离绝对值降序，相等时按数值降序
        arr.select_nth_unstable_by(k - 1, |&a, &b| {
            let dist_a = (a - median).abs();
            let dist_b = (b - median).abs();
            dist_b.cmp(&dist_a).then(b.cmp(&a))
        });

        // 保留前 k 个元素
        arr.truncate(k);
        arr
    }
}
```
