---
title: "leetcode-双指针35"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 下一个更大元素 III

给你一个正整数 n ，请你找出符合条件的最小整数，其由重新排列 n 中存在的每位数字组成，并且其值大于 n 。如果不存在这样的正整数，则返回 -1 。

注意 ，返回的整数应当是一个 32 位整数 ，如果存在满足题意的答案，但不是 32 位整数 ，同样返回 -1 。


```
impl Solution {
    pub fn next_greater_element(n: i32) -> i32 {
        let mut digits: Vec<char> = n.to_string().chars().collect();
        let len = digits.len();

        // 1. 从右向左找第一个非递减的位置（即 digits[i] < digits[i+1]）
        let mut i = len - 1;
        while i > 0 && digits[i - 1] >= digits[i] {
            i -= 1;
        }
        if i == 0 {
            return -1; // 已经是最大排列
        }
        let i = i - 1;

        // 2. 从右向左找第一个大于 digits[i] 的数
        let mut j = len - 1;
        while digits[j] <= digits[i] {
            j -= 1;
        }

        // 3. 交换这两个数
        digits.swap(i, j);

        // 4. 反转 i+1 到末尾的部分，得到最小排列
        digits[i + 1..].reverse();

        // 5. 转换为数字并检查 32 位范围
        digits.iter().collect::<String>().parse::<i32>().unwrap_or(-1)
    }
}
```
