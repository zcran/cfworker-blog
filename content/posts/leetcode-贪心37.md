---
title: "leetcode-贪心37"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 交换一次的先前排列

给你一个正整数数组 arr（可能存在重复的元素），请你返回可在 一次交换（交换两数字 arr[i] 和 arr[j] 的位置）后得到的、按字典序排列小于 arr 的最大排列。

如果无法这么操作，就请返回原数组。


```
impl Solution {
    pub fn prev_perm_opt1(mut arr: Vec<i32>) -> Vec<i32> {
        let n = arr.len();

        // 1. 从右往左找第一个满足 arr[i] > arr[i+1] 的位置 i
        //    这是能通过一次交换得到更小排列的最靠右的"变小的位置"
        let mut i = n as isize - 2;
        while i >= 0 && arr[i as usize] <= arr[i as usize + 1] {
            i -= 1;
        }
        if i < 0 {
            return arr;
        }
        let i = i as usize;

        // 2. 在 i 右侧找小于 arr[i] 的最大值；若有重复，取最靠左的那个
        let mut j = n - 1;
        while arr[j] >= arr[i] || (j > 0 && arr[j] == arr[j - 1]) {
            j -= 1;
        }

        arr.swap(i, j);
        arr
    }
}
```
