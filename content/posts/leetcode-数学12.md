---
title: "leetcode-数学12"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 负二进制转换

给你一个整数 n ，以二进制字符串的形式返回该整数的 负二进制（base -2）表示。

注意，除非字符串就是 "0"，否则返回的字符串中不能含有前导零。


```
impl Solution {
    /// 返回整数 n 的负二进制（base -2）字符串表示。
    ///
    /// 核心思路：类似二进制转换，但每一位的权值是 (-2)^k（k 从 0 开始递增，交替为 +1, -1）。
    /// 用当前位权值 k 进行试除：
    ///   - 若当前 n 为奇数，则该位为 1，并令 n -= k（因为该位贡献 k）；
    ///   - 若为偶数，则该位为 0。
    /// 然后 k *= -1 切换下一位权值符号，n /= 2 处理下一位。
    /// 由于每次减去 k 后 n 必为偶数，可以整除 2，循环必然收敛。
    pub fn base_neg2(n: i32) -> String {
        if n == 0 {
            return "0".to_string();
        }

        let mut ans = Vec::with_capacity(32);
        let mut num = n;
        let mut k = 1; // 当前位的权值 (-2)^pos，pos 从 0 开始

        while num != 0 {
            if num % 2 != 0 {
                ans.push(b'1');
                num -= k;
            } else {
                ans.push(b'0');
            }
            k *= -1;
            num /= 2;
        }

        // 低位先产生，反转后即为高位到低位的字符串
        ans.reverse();
        String::from_utf8(ans).unwrap()
    }
}
```
