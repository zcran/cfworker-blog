---
title: "leetcode-回溯23"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 累加数


累加数 是一个字符串，组成它的数字可以形成累加序列。

一个有效的 累加序列 必须 至少 包含 3 个数。除了最开始的两个数以外，序列中的每个后续数字必须是它之前两个数字之和。

给你一个只包含数字 '0'-'9' 的字符串，编写一个算法来判断给定输入是否是 累加数 。如果是，返回 true ；否则，返回 false 。

说明：累加序列里的数，除数字 0 之外，不会 以 0 开头，所以不会出现 1, 2, 03 或者 1, 02, 3 的情况。

```
impl Solution {
    pub fn is_additive_number(num: String) -> bool {
        let num = num.as_bytes();
        let n = num.len();

        // 枚举第一个数和第二个数
        for i in 1..n - 1 {
            // 第一个数不能有前导零（除非它是 "0"）
            if i > 1 && num[0] == b'0' {
                break;
            }
            let first = Self::parse_num(num, 0, i);

            for j in i + 1..n {
                // 第二个数不能有前导零（除非它是 "0"）
                if j > i + 1 && num[i] == b'0' {
                    break;
                }
                let second = Self::parse_num(num, i, j);

                // 从下一个位置开始验证累加序列
                if Self::validate(num, j, first, second) {
                    return true;
                }
            }
        }
        false
    }

    /// 从 [start, end) 解析数字
    fn parse_num(num: &[u8], start: usize, end: usize) -> i64 {
        let mut val = 0;
        for &ch in &num[start..end] {
            val = val * 10 + (ch - b'0') as i64;
        }
        val
    }

    /// 验证从当前位置开始的累加序列
    fn validate(num: &[u8], mut pos: usize, mut first: i64, mut second: i64) -> bool {
        while pos < num.len() {
            let sum = first + second;
            let sum_str = sum.to_string();
            let sum_bytes = sum_str.as_bytes();

            // 检查剩余部分是否以 sum 开头
            if pos + sum_bytes.len() > num.len() {
                return false;
            }
            if &num[pos..pos + sum_bytes.len()] != sum_bytes {
                return false;
            }

            // 移动到下一个位置，更新前两个数
            pos += sum_bytes.len();
            first = second;
            second = sum;
        }
        true
    }
}
```
