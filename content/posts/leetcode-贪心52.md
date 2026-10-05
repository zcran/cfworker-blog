---
title: "leetcode-贪心52"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 改变一个整数能得到的最大差值

给你一个整数 num 。你可以对它进行以下步骤共计 两次：

选择一个数字 x (0 <= x <= 9).
选择另一个数字 y (0 <= y <= 9) 。数字 y 可以等于 x 。
将 num 中所有出现 x 的数位都用 y 替换。

令两次对 num 的操作得到的结果分别为 a 和 b 。

请你返回 a 和 b 的 最大差值 。

注意，a 和 b 必须不能 含有前导 0，并且 不为 0。


```
impl Solution {
    pub fn max_diff(num: i32) -> i32 {
        let s = num.to_string();

        // 最大值：将第一个非 '9' 的数字全部替换为 '9'
        let max_digit = s.chars().find(|&c| c != '9');
        let max_num: i32 = match max_digit {
            Some(d) => s.replace(d, "9").parse().unwrap(),
            None => num,
        };

        // 最小值：若最高位不是 '1'，将所有最高位数字替换为 '1'；
        // 否则从第二位起找第一个非 '0' 且非最高位的数字，全部替换为 '0'
        let first = s.chars().next().unwrap();
        let min_num: i32 = if first != '1' {
            s.replace(first, "1").parse().unwrap()
        } else {
            match s[1..].chars().find(|&c| c != '0' && c != first) {
                Some(d) => s.replace(d, "0").parse().unwrap(),
                None => num,
            }
        };

        max_num - min_num
    }
}
```
