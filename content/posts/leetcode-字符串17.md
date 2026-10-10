---
title: "leetcode-字符串17"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 隐藏个人信息

给你一条个人信息字符串 s ，可能表示一个 邮箱地址 ，也可能表示一串 电话号码 。返回按如下规则 隐藏 个人信息后的结果：

电子邮件地址：

一个电子邮件地址由以下部分组成：

· 一个 名字 ，由大小写英文字母组成，后面跟着
· 一个 '@' 字符，后面跟着
· 一个 域名 ，由大小写英文字母和一个位于中间的 '.' 字符组成。'.' 不会是域名的第一个或者最后一个字符。

要想隐藏电子邮件地址中的个人信息：

· 名字 和 域名 部分的大写英文字母应当转换成小写英文字母。
· 名字 中间的字母（即，除第一个和最后一个字母外）必须用 5 个 "*****" 替换。

电话号码：

一个电话号码应当按下述格式组成：

· 电话号码可以由 10-13 位数字组成
· 后 10 位构成 本地号码
· 前面剩下的 0-3 位，构成 国家代码
· 利用 {'+', '-', '(', ')', ' '} 这些 分隔字符 按某种形式对上述数字进行分隔

要想隐藏电话号码中的个人信息：

· 移除所有 分隔字符
· 隐藏个人信息后的电话号码应该遵从这种格式：
    · "***-***-XXXX" 如果国家代码为 0 位数字
    · "+*-***-***-XXXX" 如果国家代码为 1 位数字
    · "+**-***-***-XXXX" 如果国家代码为 2 位数字
    · "+***-***-***-XXXX" 如果国家代码为 3 位数字
·"XXXX" 是最后 4 位 本地号码


```
impl Solution {
    pub fn mask_pii(s: String) -> String {
        // 国家代码位数 0..=3 对应的前缀
        const COUNTRY_CODE: [&str; 4] = ["", "+*-", "+**-", "+***-"];

        // 含 '@' 视为邮箱：保留首字母 + "*****" + 域名（全小写）
        if let Some(at) = s.find('@') {
            let mut result = String::with_capacity(s.len());
            result.push(s.as_bytes()[0].to_ascii_lowercase() as char);
            result.push_str("*****");
            result.push_str(&s[at - 1..].to_ascii_lowercase());
            return result;
        }

        // 否则视为电话：提取所有数字，保留后 4 位，前缀由国家代码位数决定
        let digits: Vec<u8> = s.bytes().filter(u8::is_ascii_digit).collect();
        let n = digits.len();
        let mut result = String::with_capacity(n + 8);
        result.push_str(COUNTRY_CODE[n - 10]);
        result.push_str("***-***-");
        result.push_str(std::str::from_utf8(&digits[n - 4..]).unwrap());
        result
    }
}
```
