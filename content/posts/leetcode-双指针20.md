---
title: "leetcode-双指针20"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 反转字符串

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 s 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。


```
impl Solution {
    /// 反转字符串（双指针法）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 左右指针交换字符，向中间移动
    pub fn reverse_string(s: &mut Vec<char>) {
        let mut left = 0;
        let mut right = s.len().saturating_sub(1);

        while left < right {
            s.swap(left, right);
            left += 1;
            right -= 1;
        }
    }
}
```
