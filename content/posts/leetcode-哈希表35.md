---
title: "leetcode-哈希表35"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## HTML 实体解析器

「HTML 实体解析器」 是一种特殊的解析器，它将 HTML 代码作为输入，并用字符本身替换掉所有这些特殊的字符实体。

HTML 里这些特殊字符和它们对应的字符实体包括：

双引号：字符实体为 &quot; ，对应的字符是 " 。
单引号：字符实体为 &apos; ，对应的字符是 ' 。
与符号：字符实体为 &amp; ，对应对的字符是 & 。
大于号：字符实体为 &gt; ，对应的字符是 > 。
小于号：字符实体为 &lt; ，对应的字符是 < 。
斜线号：字符实体为 &frasl; ，对应的字符是 / 。

给你输入字符串 text ，请你实现一个 HTML 实体解析器，返回解析器解析后的结果。


```
impl Solution {
    pub fn entity_parser(text: String) -> String {
        // 所有字符实体都以 '&' 开头、以 ';' 结尾，长度有限。
        // 遇到 '&' 时向后查找最近的 ';'，截取候选实体查表替换；
        // 不匹配则原样输出 '&'，继续扫描。
        // 用 bytes 处理保证只在 ASCII 边界操作，结果仍是合法 UTF-8。
        const TABLE: [(&str, &str); 6] = [
            ("&quot;", "\""),
            ("&apos;", "'"),
            ("&amp;", "&"),
            ("&gt;", ">"),
            ("&lt;", "<"),
            ("&frasl;", "/"),
        ];

        let bytes = text.as_bytes();
        let mut ans = String::with_capacity(text.len());
        let mut i = 0;

        while i < bytes.len() {
            if bytes[i] == b'&' {
                // 实体最长 7 字符（&frasl;），限定查找范围避免大段扫描。
                let end = (i + 8).min(bytes.len());
                if let Some(pos) = bytes[i..end].iter().position(|&b| b == b';') {
                    let candidate = &text[i..=i + pos];
                    if let Some(&(_, repl)) = TABLE.iter().find(|&&(k, _)| k == candidate) {
                        ans.push_str(repl);
                        i += pos + 1;
                        continue;
                    }
                }
            }
            // 非实体或未匹配：按字符原样复制（处理多字节 UTF-8）。
            let ch = text[i..].chars().next().unwrap();
            ans.push(ch);
            i += ch.len_utf8();
        }

        ans
    }
}
```
