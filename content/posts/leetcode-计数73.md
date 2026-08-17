---
title: "leetcode-计数73"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 最大回文数字

给你一个仅由数字（0 - 9）组成的字符串 num 。

请你找出能够使用 num 中数字形成的 最大回文 整数，并以字符串形式返回。该整数不含 前导零 。

注意：

你 无需 使用 num 中的所有数字，但你必须使用 至少 一个数字。

数字可以重新排序。


```
impl Solution {
    pub fn largest_palindromic(num: String) -> String {
        // 统计每个数字（0-9）的出现次数
        let mut count = [0; 10];
        for &b in num.as_bytes() {
            count[(b - b'0') as usize] += 1;
        }

        // 构建回文的左半部分（从大到小，数字越大越靠前）
        let mut half = Vec::with_capacity(num.len() / 2 + 1);
        let mut middle = None;

        // 从 9 到 0 遍历，优先使用大数字
        for digit in (0..=9).rev() {
            let pairs = count[digit] / 2;
            if pairs > 0 {
                // 如果左半为空且当前数字是 0，说明只有 0 可用
                if half.is_empty() && digit == 0 {
                    let has_other = count.iter().skip(1).any(|&c| c > 0);
                    if !has_other {
                        return "0".to_string(); // 全零情况
                    }
                    break; // 不能以 0 开头，放弃所有 0 配对
                }
                // 将 digit 对应的 ASCII 字符添加 pairs 次到左半
                let ch = b'0' + digit as u8;
                half.extend(std::iter::repeat(ch).take(pairs as usize));
            }

            // 如果中间位未设置且该数字有剩余（奇数个），选最大的作为中间位
            if middle.is_none() && count[digit] % 2 == 1 {
                middle = Some(b'0' + digit as u8);
            }
        }

        // 如果没有任何数字可用（理论上不会发生，但保险）
        if half.is_empty() && middle.is_none() {
            return "0".to_string();
        }

        // 右半部分是左半部分的逆序
        let mut right = half.clone();
        right.reverse();

        // 组装结果：左半 + 中间位（如果有）+ 右半
        let mut result = Vec::with_capacity(half.len() * 2 + 1);
        result.extend(&half);
        if let Some(m) = middle {
            result.push(m);
        }
        result.extend(&right);

        // 转换为字符串
        String::from_utf8(result).unwrap()
    }
}
```
