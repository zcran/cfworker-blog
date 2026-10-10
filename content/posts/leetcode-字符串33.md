---
title: "leetcode-字符串33"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 重新格式化电话号码

给你一个字符串形式的电话号码 number 。number 由数字、空格 ' '、和破折号 '-' 组成。

请你按下述方式重新格式化电话号码。

· 首先，删除 所有的空格和破折号。
· 其次，将数组从左到右 每 3 个一组 分块，直到 剩下 4 个或更少数字。剩下的数字将按下述规定再分块：
    · 2 个数字：单个含 2 个数字的块。
    · 3 个数字：单个含 3 个数字的块。
    · 4 个数字：两个分别含 2 个数字的块。

最后用破折号将这些块连接起来。注意，重新格式化过程中 不应该 生成仅含 1 个数字的块，并且 最多 生成两个含 2 个数字的块。

返回格式化后的电话号码。


```
impl Solution {
    pub fn reformat_number(number: String) -> String {
        // 先收集所有数字
        let digits: Vec<u8> = number.bytes().filter(|b| b.is_ascii_digit()).collect();
        let n = digits.len();
        let mut result = String::with_capacity(n + n / 3);

        // 从头每 3 位一组输出，直到剩余不超过 4 位
        let mut i = 0;
        while n - i > 4 {
            result.push(digits[i] as char);
            result.push(digits[i + 1] as char);
            result.push(digits[i + 2] as char);
            result.push('-');
            i += 3;
        }

        // 处理末尾剩余：2 位 -> 一组；3 位 -> 一组；4 位 -> 两组各 2 位
        let rem = n - i;
        if rem == 4 {
            result.push(digits[i] as char);
            result.push(digits[i + 1] as char);
            result.push('-');
            result.push(digits[i + 2] as char);
            result.push(digits[i + 3] as char);
        } else {
            for j in 0..rem {
                result.push(digits[i + j] as char);
            }
        }

        result
    }
}
```
