---
title: "leetcode-字符串52"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 价格减免

句子 是由若干个单词组成的字符串，单词之间用单个空格分隔，其中每个单词可以包含数字、小写字母、和美元符号 '$' 。如果单词的形式为美元符号后跟着一个非负实数，那么这个单词就表示一个 价格 。

例如 "$100"、"$23" 和 "$6" 表示价格，而 "100"、"$" 和 "$1e5 不是。
给你一个字符串 sentence 表示一个句子和一个整数 discount 。对于每个表示价格的单词，都在价格的基础上减免 discount% ，并 更新 该单词到句子中。所有更新后的价格应该表示为一个 恰好保留小数点后两位 的数字。

返回表示修改后句子的字符串。

注意：所有价格 最多 为 10 位数字。




```
impl Solution {
    pub fn discount_prices(sentence: String, discount: i32) -> String {
        // 折扣后乘以的系数，用整数运算避免浮点误差：价格最多 10 位，可用 i64
        let factor = (100 - discount) as i64;

        sentence
            .split_ascii_whitespace()
            .map(|w| {
                // 形如 $<digits> 的才视为价格
                let bytes = w.as_bytes();
                if bytes.len() > 1 && bytes[0] == b'$' && bytes[1..].iter().all(u8::is_ascii_digit) {
                    let price: i64 = w[1..].parse().unwrap();
                    // 计算折扣价（单位：分），四舍五入到两位小数
                    let cents = (price * factor * 100 + 50) / 100;
                    format!("${}.{:02}", cents / 100, cents % 100)
                } else {
                    w.to_string()
                }
            })
            .collect::<Vec<_>>()
            .join(" ")
    }
}
```
