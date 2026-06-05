---
title: "leetcode-递归15"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 整数转换英文表示

将非负整数 num 转换为其对应的英文表示。


```
impl Solution {
    /// 将非负整数转换为英文表示
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::number_to_words(123), "One Hundred Twenty Three".to_string());
    /// ```
    pub fn number_to_words(num: i32) -> String {
        if num == 0 {
            return "Zero".to_string();
        }

        // 词库：使用静态数组，零开销
        const SINGLES: [&str; 10] = [
            "", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine",
        ];
        const TEENS: [&str; 10] = [
            "Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen",
            "Sixteen", "Seventeen", "Eighteen", "Nineteen",
        ];
        const TENS: [&str; 10] = [
            "", "Ten", "Twenty", "Thirty", "Forty", "Fifty",
            "Sixty", "Seventy", "Eighty", "Ninety",
        ];
        const THOUSANDS: [&str; 4] = ["", "Thousand", "Million", "Billion"];

        /// 递归处理三位数以内的数字（内部函数，闭包捕获词库）
        fn convert_three_digits(
            mut num: i32,
            output: &mut Vec<String>,
            singles: &[&str; 10],
            teens: &[&str; 10],
            tens: &[&str; 10],
        ) {
            if num >= 100 {
                output.push(singles[(num / 100) as usize].to_string());
                output.push("Hundred".to_string());
                num %= 100;
            }

            if num >= 20 {
                output.push(tens[(num / 10) as usize].to_string());
                num %= 10;
            } else if num >= 10 {
                output.push(teens[(num - 10) as usize].to_string());
                return; // teens 已经包含个位，直接返回
            }

            if num > 0 {
                output.push(singles[num as usize].to_string());
            }
        }

        let mut result = Vec::with_capacity(32); // 预分配容量，避免多次扩容
        let mut remaining = num;

        // 从高位到低位处理（十亿 → 百万 → 千 → 个位）
        for (i, &unit_name) in THOUSANDS.iter().enumerate().rev() {
            let unit = 1000_i32.pow(i as u32); // 1000^3=1B, 1000^2=1M, 1000^1=1K, 1000^0=1
            let cur = remaining / unit;

            if cur != 0 {
                remaining %= unit;
                convert_three_digits(cur, &mut result, &SINGLES, &TEENS, &TENS);

                if !unit_name.is_empty() {
                    result.push(unit_name.to_string());
                }
            }
        }

        // 使用 join 高效拼接，避免反复分配字符串
        result.join(" ")
    }
}
```
