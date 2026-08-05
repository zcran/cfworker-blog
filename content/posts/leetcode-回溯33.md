---
title: "leetcode-回溯33"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 将数组拆分成斐波那契序列

给定一个数字字符串 num，比如 "123456579"，我们可以将它分成「斐波那契式」的序列 [123, 456, 579]。

形式上，斐波那契式 序列是一个非负整数列表 f，且满足：

· 0 <= f[i] < 231 ，（也就是说，每个整数都符合 32 位 有符号整数类型）
· f.length >= 3
· 对于所有的0 <= i < f.length - 2，都有 f[i] + f[i + 1] = f[i + 2]

另外，请注意，将字符串拆分成小块时，每个块的数字一定不要以零开头，除非这个块是数字 0 本身。

返回从 num 拆分出来的任意一组斐波那契式的序列块，如果不能拆分则返回 []。


```
impl Solution {
    /// 将字符串拆分成斐波那契式序列
    ///
    /// 思路：回溯法，枚举前两个数，然后验证后续是否满足斐波那契性质
    pub fn split_into_fibonacci(s: String) -> Vec<i32> {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut path = Vec::new();

        /// 回溯搜索
        /// - start: 当前处理的起始位置
        /// - path: 已选择的数字序列
        fn backtrack(bytes: &[u8], start: usize, path: &mut Vec<i32>) -> bool {
            let n = bytes.len();

            // 到达末尾：序列长度至少为 3 才合法
            if start == n {
                return path.len() >= 3;
            }

            // 处理前导零：如果当前字符是 '0'，只能选择单独的数字 0
            let max_len = if bytes[start] == b'0' { 1 } else { n - start };

            // 需要匹配的目标值（前两个数之和）
            let target = if path.len() >= 2 {
                Some(path[path.len() - 1] + path[path.len() - 2])
            } else {
                None
            };

            let mut num: i64 = 0;

            for i in start..start + max_len {
                num = num * 10 + (bytes[i] - b'0') as i64;

                // 超过 32 位整数范围，提前终止
                if num > i32::MAX as i64 {
                    break;
                }

                let cur = num as i32;

                // 如果已经有前两个数，则必须匹配 target
                if let Some(t) = target {
                    if cur > t {
                        break; // 当前数已超过目标，后续更大，剪枝
                    }
                    if cur != t {
                        continue; // 不匹配，尝试下一位
                    }
                }

                // 选择当前数字
                path.push(cur);
                if backtrack(bytes, i + 1, path) {
                    return true;
                }
                path.pop();
            }

            false
        }

        backtrack(bytes, 0, &mut path);
        path
    }
}
```
