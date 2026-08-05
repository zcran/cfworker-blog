---
title: "leetcode-栈20"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 比较含退格的字符串

给定 s 和 t 两个字符串，当它们分别被输入到空白的文本编辑器后，如果两者相等，返回 true 。# 代表退格字符。

注意：如果对空文本输入退格字符，文本继续为空。


```
// 退格字符串比较
// 使用双指针从后向前遍历，无需额外内存
impl Solution {
    pub fn backspace_compare(s: String, t: String) -> bool {
        let (s_bytes, t_bytes) = (s.as_bytes(), t.as_bytes());
        let (mut i, mut j) = (s_bytes.len() as i32 - 1, t_bytes.len() as i32 - 1);

        while i >= 0 || j >= 0 {
            let skip = |bytes: &[u8], pos: &mut i32| {
                let mut backspaces = 0;
                while *pos >= 0 {
                    if bytes[*pos as usize] == b'#' {
                        backspaces += 1;
                        *pos -= 1;
                    } else if backspaces > 0 {
                        backspaces -= 1;
                        *pos -= 1;
                    } else {
                        break;
                    }
                }
            };

            skip(s_bytes, &mut i);
            skip(t_bytes, &mut j);

            let char_s = if i >= 0 { Some(s_bytes[i as usize]) } else { None };
            let char_t = if j >= 0 { Some(t_bytes[j as usize]) } else { None };

            if char_s != char_t {
                return false;
            }

            if char_s.is_none() && char_t.is_none() {
                return true;
            }

            i -= 1;
            j -= 1;
        }

        true
    }
}
```
