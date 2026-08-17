---
title: "leetcode-模拟32"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 将二进制表示减到 1 的步骤数

给你一个以二进制形式表示的数字 s 。请你返回按下述规则将其减少到 1 所需要的步骤数：

如果当前数字为偶数，则将其除以 2 。

如果当前数字为奇数，则将其加上 1 。

题目保证你总是可以按上述规则将测试用例变为 1 。


```
impl Solution {
    /// 将二进制字符串按规则减到 1 所需的步数
    /// 规则：偶数除以2（右移），奇数加1
    /// 核心优化：用位运算模拟二进制加法，避免逐位操作字符串
    pub fn num_steps(s: String) -> i32 {
        let mut bytes = s.into_bytes();
        let mut steps = 0;

        while bytes.len() > 1 || bytes[0] != b'1' {
            steps += 1;
            let last = bytes.len() - 1;

            if bytes[last] == b'0' {
                // 偶数：右移一位（去掉末尾的 0）
                bytes.pop();
            } else {
                // 奇数：加 1
                // 从低位向高位找第一个 0，将其变为 1，后面所有 1 变 0
                // 如果全是 1，则在最高位前插入 1
                let mut carry = true;
                for i in (0..=last).rev() {
                    if bytes[i] == b'1' {
                        bytes[i] = b'0';
                    } else {
                        bytes[i] = b'1';
                        carry = false;
                        break;
                    }
                }
                if carry {
                    bytes.insert(0, b'1');
                }
            }
        }

        steps
    }
}
```
