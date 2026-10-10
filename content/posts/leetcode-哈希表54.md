---
title: "leetcode-哈希表54"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 解密消息

给你字符串 key 和 message ，分别表示一个加密密钥和一段加密消息。解密 message 的步骤如下：

1. 使用 key 中 26 个英文小写字母第一次出现的顺序作为替换表中的字母 顺序 。
2. 将替换表与普通英文字母表对齐，形成对照表。
3. 按照对照表 替换 message 中的每个字母。
4. 空格 ' ' 保持不变。

例如，key = "happy boy"（实际的加密密钥会包含字母表中每个字母 至少一次），据此，可以得到部分对照表（'h' -> 'a'、'a' -> 'b'、'p' -> 'c'、'y' -> 'd'、'b' -> 'e'、'o' -> 'f'）。

返回解密后的消息。


```
impl Solution {
    pub fn decode_message(key: String, message: String) -> String {
        // table[i] 表示密文字母 ('a' + i) 对应的明文字母
        let mut table = [0u8; 26];
        let mut next = b'a'; // 下一个待分配的明文字母

        // 按 key 中字母首次出现的顺序建立替换表
        for &b in key.as_bytes() {
            if b == b' ' {
                continue;
            }
            let idx = (b - b'a') as usize;
            if table[idx] == 0 {
                table[idx] = next;
                next += 1;
                if next > b'z' {
                    break; // 26 个字母已全部映射完毕
                }
            }
        }

        // 按替换表逐字节解密，空格原样保留
        message
            .bytes()
            .map(|b| if b == b' ' { b' ' } else { table[(b - b'a') as usize] })
            .map(char::from)
            .collect()
    }
}
```
