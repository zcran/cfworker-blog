---
title: "leetcode-双指针59"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 煎饼排序

给你一个整数数组 arr ，请使用 煎饼翻转 完成对数组的排序。

一次煎饼翻转的执行过程如下：

选择一个整数 k ，1 <= k <= arr.length
反转子数组 arr[0...k-1]（下标从 0 开始）

例如，arr = [3,2,1,4] ，选择 k = 3 进行一次煎饼翻转，反转子数组 [3,2,1] ，得到 arr = [1,2,3,4] 。

以数组形式返回能使 arr 有序的煎饼翻转操作所对应的 k 值序列。任何将数组排序且翻转次数在 10 * arr.length 范围内的有效答案都将被判断为正确。


```
impl Solution {
    pub fn pancake_sort(mut arr: Vec<i32>) -> Vec<i32> {
        let mut result = Vec::new();
        let n = arr.len();

        // 从大到小处理每个位置
        for target_pos in (0..n).rev() {
            // 找到当前未排序部分的最大值位置
            let mut max_idx = 0;
            for i in 1..=target_pos {
                if arr[i] > arr[max_idx] {
                    max_idx = i;
                }
            }

            // 如果最大值已经在目标位置，跳过
            if max_idx == target_pos {
                continue;
            }

            // 如果最大值不在第一个位置，先翻转到开头
            if max_idx != 0 {
                arr[0..=max_idx].reverse();
                result.push(max_idx as i32 + 1);
            }

            // 然后翻转到目标位置
            arr[0..=target_pos].reverse();
            result.push(target_pos as i32 + 1);
        }

        result
    }
}
```
