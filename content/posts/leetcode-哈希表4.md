---
title: "leetcode-哈希表4"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 分数到小数

给定两个整数，分别表示分数的分子 numerator 和分母 denominator，以 字符串形式返回小数 。

如果小数部分为循环小数，则将循环的部分括在括号内。

如果存在多个答案，只需返回 任意一个 。

对于所有给定的输入，保证 答案字符串的长度小于 10^4 。

注意，如果分数可以表示为有限长度的字符串，则 必须 返回它。


```
impl Solution {
    pub fn fraction_to_decimal(numerator: i32, denominator: i32) -> String {
        // 0 直接返回 "0"，避免出现 "-0"。
        if numerator == 0 {
            return "0".to_string();
        }

        // 用 i64 避免 i32::MIN 取绝对值时溢出。
        let mut a = numerator as i64;
        let mut b = denominator as i64;

        // 确定符号，后续用绝对值运算。
        let sign = if (a < 0) ^ (b < 0) { "-" } else { "" };
        a = a.abs();
        b = b.abs();

        // 整数部分与余数。
        let int_part = a / b;
        let mut rem = a % b;
        if rem == 0 {
            return format!("{}{}", sign, int_part);
        }

        // 小数部分：余数每次乘 10 取商；余数重复出现即进入循环节。
        let mut ans = format!("{}{}.", sign, int_part);
        let mut seen = std::collections::HashMap::new();
        seen.insert(rem, ans.len());

        while rem != 0 {
            rem *= 10;
            ans.push((b'0' + (rem / b) as u8) as char);
            rem %= b;

            match seen.get(&rem) {
                // 余数出现过，从记录位置插入括号。
                Some(&pos) => return format!("{}({})", &ans[..pos], &ans[pos..]),
                None => { seen.insert(rem, ans.len()); }
            }
        }
        ans
    }
}
```
