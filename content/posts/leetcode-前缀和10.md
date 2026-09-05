---
title: "leetcode-前缀和10"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 字母移位

有一个由小写字母组成的字符串 s，和一个长度相同的整数数组 shifts。

我们将字母表中的下一个字母称为原字母的 移位 shift() （由于字母表是环绕的， 'z' 将会变成 'a'）。

例如，shift('a') = 'b', shift('t') = 'u', 以及 shift('z') = 'a'。

对于每个 shifts[i] = x ， 我们会将 s 中的前 i + 1 个字母移位 x 次。

返回 将所有这些移位都应用到 s 后最终得到的字符串 。




```
impl Solution {
    pub fn shifting_letters(s: String, mut shifts: Vec<i32>) -> String {
        let n = s.len();
        let bytes = s.as_bytes();
        let mut result = Vec::with_capacity(n);

        // 从后向前累积移位量，shifts[i] 表示第 i 个字符需要移动的总次数
        let mut cumulative = 0;
        for i in (0..n).rev() {
            cumulative = (cumulative + shifts[i]) % 26;
            // 计算当前字符移位后的结果
            let shifted = (bytes[i] - b'a' + cumulative as u8) % 26 + b'a';
            result.push(shifted);
        }

        // 由于是从后向前遍历，结果需要反转
        result.reverse();
        String::from_utf8(result).unwrap()
    }
}
```
