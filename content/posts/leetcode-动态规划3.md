---
title: "leetcode-动态规划3"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 解码方法

一条包含字母 A-Z 的消息通过以下映射进行了 编码 ：

"1" -> 'A'

"2" -> 'B'

...

"25" -> 'Y'

"26" -> 'Z'

然而，在 解码 已编码的消息时，你意识到有许多不同的方式来解码，因为有些编码被包含在其它编码当中（"2" 和 "5" 与 "25"）。

例如，"11106" 可以映射为：

· "AAJF" ，将消息分组为 (1, 1, 10, 6)
· "KJF" ，将消息分组为 (11, 10, 6)
· 消息不能分组为  (1, 11, 06) ，因为 "06" 不是一个合法编码（只有 "6" 是合法的）。

注意，可能存在无法解码的字符串。

给你一个只含数字的 非空 字符串 s ，请计算并返回 解码 方法的 总数 。如果没有合法的方式解码整个字符串，返回 0。

题目数据保证答案肯定是一个 32 位 的整数。


```
impl Solution {
    pub fn num_decodings(s: String) -> i32 {
        let bytes = s.as_bytes();
        if bytes[0] == b'0' {
            return 0;
        }

        // pre = dp[i-2], cur = dp[i-1]
        let (mut pre, mut cur) = (1, 1);
        for i in 1..bytes.len() {
            let (a, b) = (bytes[i - 1], bytes[i]);

            // 无法解码：当前位是 '0' 且不能与前一位组成合法编码
            if b == b'0' && a != b'1' && a != b'2' {
                return 0;
            }

            // 当前位为 '0'：只能两位一起解码，dp[i] = dp[i-2]
            if b == b'0' {
                let t = pre;
                pre = cur;
                cur = t;
            }
            // 能与前一位组成两位数（10~26）：dp[i] = dp[i-1] + dp[i-2]
            else if a == b'1' || (a == b'2' && b <= b'6') {
                let t = cur + pre;
                pre = cur;
                cur = t;
            }
            // 只能单独解码当前位：dp[i] = dp[i-1]
            else {
                pre = cur;
            }
        }

        cur
    }
}
```
