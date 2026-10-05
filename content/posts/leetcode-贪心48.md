---
title: "leetcode-贪心48"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 形成三的最大倍数

给你一个整数数组 digits，你可以通过按 任意顺序 连接其中某些数字来形成 3 的倍数，请你返回所能得到的最大的 3 的倍数。

由于答案可能不在整数数据类型范围内，请以字符串形式返回答案。如果无法得到答案，请返回一个空字符串。返回的结果不应包含不必要的前导零。


```
impl Solution {
    pub fn largest_multiple_of_three(digits: Vec<i32>) -> String {
        let mut digits = digits;
        digits.sort_unstable();

        let rem = digits.iter().sum::<i32>() % 3;

        // 删除一个模 3 余 target 的数字；若不存在返回 false
        let remove_one = |digits: &mut Vec<i32>, target: i32| -> bool {
            if let Some(pos) = digits.iter().position(|&d| d % 3 == target) {
                digits.remove(pos);
                true
            } else {
                false
            }
        };

        if rem != 0 {
            // 优先删 1 个；失败则删 2 个余数为 (3 - rem) 的数字
            let target = rem;
            let other = 3 - rem;
            let ok = remove_one(&mut digits, target)
                || (|| {
                    let mut removed = 0;
                    let mut i = 0;
                    while i < digits.len() && removed < 2 {
                        if digits[i] % 3 == other {
                            digits.remove(i);
                            removed += 1;
                        } else {
                            i += 1;
                        }
                    }
                    removed == 2
                })();
            if !ok {
                return String::new();
            }
        }

        if digits.is_empty() {
            return String::new();
        }

        // 所有数字均为 0 时，只保留一个 0
        if digits.last() == Some(&0) {
            return "0".to_string();
        }

        // 升序排列后反转即为最大数（0 自然位于末尾，不影响数值）
        digits
            .iter()
            .rev()
            .map(|&d| (d as u8 + b'0') as char)
            .collect()
    }
}
```
