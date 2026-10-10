---
title: "leetcode-字符串8"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 验证IP地址

给定一个字符串 queryIP。如果是有效的 IPv4 地址，返回 "IPv4" ；如果是有效的 IPv6 地址，返回 "IPv6" ；如果不是上述类型的 IP 地址，返回 "Neither" 。

有效的IPv4地址 是 “x1.x2.x3.x4” 形式的IP地址。 其中 0 <= xi <= 255 且 xi 不能包含 前导零。例如: “192.168.1.1” 、 “192.168.1.0” 为有效IPv4地址， “192.168.01.1” 为无效IPv4地址; “192.168.1.00” 、 “192.168@1.1” 为无效IPv4地址。

一个有效的IPv6地址 是一个格式为“x1:x2:x3:x4:x5:x6:x7:x8” 的IP地址，其中:

· 1 <= xi.length <= 4
· xi 是一个 十六进制字符串 ，可以包含数字、小写英文字母( 'a' 到 'f' )和大写英文字母( 'A' 到 'F' )。
· 在 xi 中允许前导零。

例如 "2001:0db8:85a3:0000:0000:8a2e:0370:7334" 和 "2001:db8:85a3:0:0:8A2E:0370:7334" 是有效的 IPv6 地址，而 "2001:0db8:85a3::8A2E:037j:7334" 和 "02001:0db8:85a3:0000:0000:8a2e:0370:7334" 是无效的 IPv6 地址。


```
impl Solution {
    pub fn valid_ip_address(query_ip: String) -> String {
        // 含 '.' 则按 IPv4 校验，否则按 IPv6 校验
        if query_ip.contains('.') {
            if Self::is_ipv4(&query_ip) { "IPv4" } else { "Neither" }.to_string()
        } else {
            if Self::is_ipv6(&query_ip) { "IPv6" } else { "Neither" }.to_string()
        }
    }

    // IPv4：恰好 4 段，每段 1~3 位纯数字、无前导零、值 <= 255
    fn is_ipv4(s: &str) -> bool {
        let parts: Vec<&str> = s.split('.').collect();
        if parts.len() != 4 {
            return false;
        }
        parts.iter().all(|p| {
            let b = p.as_bytes();
            !b.is_empty()
                && b.len() <= 3
                && b.iter().all(u8::is_ascii_digit)
                && (b.len() == 1 || b[0] != b'0') // 无前导零（"0" 本身允许）
                && p.parse::<u16>().map_or(false, |v| v <= 255)
        })
    }

    // IPv6：恰好 8 段，每段 1~4 位十六进制字符
    fn is_ipv6(s: &str) -> bool {
        let parts: Vec<&str> = s.split(':').collect();
        if parts.len() != 8 {
            return false;
        }
        parts.iter().all(|p| {
            let b = p.as_bytes();
            !b.is_empty() && b.len() <= 4 && b.iter().all(u8::is_ascii_hexdigit)
        })
    }
}
```
